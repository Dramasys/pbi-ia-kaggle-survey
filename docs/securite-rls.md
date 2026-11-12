# Phase 1.8 : Sécurité (RLS) - Projet IA (Kaggle Survey)

## Objectif
Les données de l'enquête sont publiques : la sécurité par ligne est donc un exercice, mais un scénario réaliste. Un analyste régional ne voit que les répondants des pays de sa zone. Le rôle est dynamique : le périmètre dépend de l'identité de la personne connectée, lue dans une table de sécurité.

## Modèle de sécurité
* **Table `Securite_Region`** (colonnes `Email` et `Region`), chargée depuis `data/reference/securite_region.csv`. Elle n'a **aucune relation** avec le reste du modèle et est masquée.
* **Région** : colonne `Dim_Pays[Region]` (12 régions plus « Non précisé »). Le filtre se propage de `Dim_Pays` vers les deux tables de faits grâce aux relations un-à-plusieurs unidirectionnelles.

| Rôle | Filtres | Usage |
|---|---|---|
| `Role_Region` | `Dim_Pays` : voir la formule ci-dessous ; `Securite_Region` : `FALSE()` | Analystes régionaux |
| `Role_Global` | Aucun | Administrateurs fonctionnels |

Filtre DAX de `Role_Region` sur la table `Dim_Pays` :
```dax
[Region] IN
    SELECTCOLUMNS (
        FILTER ( Securite_Region, Securite_Region[Email] = USERPRINCIPALNAME () ),
        "r", Securite_Region[Region]
    )
```
Le filtre `FALSE()` sur `Securite_Region` empêche tout utilisateur du rôle de lire la liste des adresses et des régions.

## Utilisateurs de test (fictifs, domaine réservé `example.com`)
| Utilisateur | Régions autorisées |
|---|---|
| analyste.europe@example.com | Europe de l'Ouest et du Nord, Europe du Sud |
| analyste.asie@example.com | Asie du Sud |
| analyste.amerique@example.com | Amérique du Nord |

## Plan de test (Modélisation > Afficher en tant que)
Les valeurs sont celles de la mesure `Nb Repondants` par édition (2020, 2021, 2022) et de `Nb Pays`.

| Test | Rôle et utilisateur | Nb Repondants (2020 / 2021 / 2022) | Nb Pays (2020 / 2021 / 2022) |
|---|---|---|---|
| T1 | `Role_Region` : analyste.europe@example.com | 2 470 / 3 005 / 1 357 | 12 / 15 / 9 |
| T2 | `Role_Region` : analyste.asie@example.com | 6 411 / 8 462 / 9 815 | 5 / 5 / 5 |
| T3 | `Role_Region` : analyste.amerique@example.com | 2 538 / 2 981 / 3 177 | 2 / 2 / 2 |
| T4 | `Role_Region` : adresse absente de la table | Aucune donnée | Aucune donnée |
| T5 | `Role_Global` | 20 036 / 25 973 / 23 997 | 53 / 64 / 56 |
| T6 | `Role_Region` : analyste.asie@example.com, fiche pays de l'Inde | Part des répondants : 89,4 % (contre 31,5 % sans sécurité) | |

Vérifications complémentaires : la liste du segment « Pays » ne contient que les pays autorisés (15 pays pour l'analyste Europe, 5 pour l'Asie, 2 pour l'Amérique) ; la carte de la page « Vue d'ensemble » n'affiche que ces pays.

## Comportements à connaître
* **Le « monde » devient le périmètre autorisé.** Les mesures `Part du Pays` et `% Utilisation Monde` retirent le filtre pays, mais ne peuvent pas retirer la sécurité : pour un analyste régional, elles se comparent à sa région, pas au monde entier (test T6).
* **Les autres dimensions ne sont pas filtrées** : les listes des segments (âge, métier…) montrent toutes leurs valeurs, mais les mesures restent vides pour les valeurs hors périmètre. Aucun effectif n'est exposé.
* **Service Power BI** : la sécurité par ligne ne s'applique qu'aux membres avec le rôle Viewer de l'espace de travail ; les rôles Admin, Member et Contributor voient toutes les données. L'affectation des personnes aux rôles se fait dans les paramètres de sécurité du modèle sémantique. Ce projet n'est pas publié : les tests sont réalisés dans Power BI Desktop uniquement.
