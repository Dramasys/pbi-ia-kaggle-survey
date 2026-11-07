# Phase 1.6 : Registre des mesures DAX - Projet IA (Kaggle Survey)

Toutes les mesures sont dans la table `_Mesures`. Conventions : `DIVIDE` pour toute division, `VAR ... RETURN` quand une expression est longue ou répétée, commentaire `//` placé **après** la première ligne (le nom de la mesure doit être en tête dans la barre de formule). Les colonnes ne sont jamais totalisées : tous les calculs passent par des mesures.

## Avancement
| Mesure | Dossier | Statut |
|---|---|---|
| Nb Repondants | Volumes | Implémentée |
| Nb Utilisateurs | Volumes | Implémentée |
| Nb Repondants Question | Volumes | Implémentée |
| % Utilisation | Parts | Implémentée |
| Nb Pays | Volumes | Prévue |
| Outil N1 | Parts | Prévue |
| Nb Avec Salaire, Taux Reponse Salaire | Salaires | Prévue |
| Salaire Moyen Estime, Salaire Median Estime, Salaire Moyen Fiable | Salaires | Prévue |
| % Utilisation A-1, Evolution pts, Rang Outil | Tendances | Prévue |
| % Utilisation Monde, Ecart vs Monde pts, % Experimentes, % Repondants | Parts, Salaires | Prévue |

## Dossier Volumes

### Nb Repondants
* **Objectif :** nombre de répondants de la sélection (base de tous les pourcentages).
* **Format :** nombre entier, séparateur de milliers.
```dax
Nb Repondants = COUNTROWS ( Fact_Repondants )
```
* **Contrôle :** 20 036 (2020), 25 973 (2021), 23 997 (2022), 70 006 au total.

### Nb Utilisateurs
* **Objectif :** nombre de répondants distincts ayant coché l'option sélectionnée (un répondant n'est compté qu'une fois, même s'il a coché plusieurs variantes regroupées, comme les trois libellés Jupyter).
```dax
Nb Utilisateurs = DISTINCTCOUNT ( Fact_Usage_Outils[RepondantID] )
```
* **Contrôle (thème Langages) :** Python 15 530, 21 860 et 18 653 ; SQL 7 535, 10 756 et 9 620 ; R 4 277, 5 334 et 4 571 (2020, 2021, 2022).

### Nb Repondants Question
* **Objectif :** base du pourcentage, c'est-à-dire les répondants ayant répondu à la question du thème (l'outil est retiré du filtre, le thème est conservé).
```dax
Nb Repondants Question =
// REMOVEFILTERS sur Option : on garde le thème, on retire l'outil choisi
CALCULATE ( [Nb Utilisateurs], REMOVEFILTERS ( Dim_Reponse[Option] ) )
```
* **Piège :** n'a de sens que si un seul thème est sélectionné.

## Dossier Parts

### % Utilisation
* **Objectif :** part des répondants à la question qui utilisent l'outil.
* **Format :** pourcentage, 1 décimale.
```dax
% Utilisation = DIVIDE ( [Nb Utilisateurs], [Nb Repondants Question] )
```
* **Contrôle :** Python 86,7 % (2020), 87,6 % (2021), 88,3 % (2022).

## Mesures prévues
Elles seront ajoutées ici, avec leur formule et leur contrôle, au fur et à mesure de leur création.