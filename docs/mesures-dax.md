## Registre des mesures DAX - Projet IA (Kaggle Survey)

Toutes les mesures sont dans la table `_Mesures`. Conventions : `DIVIDE` pour toute division, `VAR ... RETURN` quand une expression est longue ou répétée, commentaire `//` placé **après** la première ligne (le nom de la mesure doit être en tête dans la barre de formule). Les colonnes ne sont jamais totalisées (« Totaliser par = Aucun ») : tous les calculs passent par des mesures.

## Avancement

| Mesure | Dossier | Statut |
|---|---|---|
| Nb Repondants, Nb Utilisateurs, Nb Repondants Question, Nb Pays | Volumes | Implémentée |
| % Utilisation, Outil N1 | Parts | Implémentée |
| Nb Avec Salaire, Taux Reponse Salaire | Salaires | Implémentée |
| Salaire Moyen Estime, Salaire Median Estime, Salaire Moyen Fiable | Salaires | Implémentée |
| % Utilisation A-1, Evolution pts, Rang Outil | Tendances | Prévue |
| % Utilisation Monde, Ecart vs Monde pts, % Experimentes, % Repondants | Parts, Salaires | Prévue |

---

## Dossier Volumes

### Nb Repondants
* **Objectif :** nombre de répondants de la sélection (base de tous les pourcentages).
* **Format :** nombre entier, séparateur de milliers.

```dax
Nb Repondants = COUNTROWS ( Fact_Repondants )
```

* **Contrôle :** 20 036 (2020), 25 973 (2021), 23 997 (2022), 70 006 au total.

### Nb Utilisateurs
* **Objectif :** nombre de répondants distincts ayant coché l'option sélectionnée. Un répondant n'est compté qu'une fois, même s'il a coché plusieurs variantes regroupées (par exemple les trois libellés Jupyter).
* **Format :** nombre entier, séparateur de milliers.

```dax
Nb Utilisateurs = DISTINCTCOUNT ( Fact_Usage_Outils[RepondantID] )
```

* **Contrôle (thème Langages) :** Python 15 530, 21 860 et 18 653 ; SQL 7 535, 10 756 et 9 620 ; R 4 277, 5 334 et 4 571 (2020, 2021, 2022).

### Nb Repondants Question
* **Objectif :** base du pourcentage, c'est-à-dire les répondants ayant répondu à la question du thème. L'outil est retiré du filtre, le thème est conservé.
* **Format :** nombre entier, séparateur de milliers.

```dax
Nb Repondants Question =
// REMOVEFILTERS sur Option : on garde le thème, on retire l'outil choisi
CALCULATE ( [Nb Utilisateurs], REMOVEFILTERS ( Dim_Reponse[Option] ) )
```

* **Piège :** n'a de sens que si un seul thème est sélectionné.

### Nb Pays
* **Objectif :** nombre de pays réels de la sélection (hors « Autre pays » et « Non communiqué »).
* **Format :** nombre entier.

```dax
Nb Pays =
// Pays réels uniquement
CALCULATE ( DISTINCTCOUNT ( Fact_Repondants[PaysKey] ), Dim_Pays[EstPaysReel] = "Oui" )
```

* **Contrôle :** 53 (2020), 64 (2021), 56 (2022), 66 au total.

---

## Dossier Parts

### % Utilisation
* **Objectif :** part des répondants à la question qui utilisent l'outil.
* **Format :** pourcentage, 1 décimale.

```dax
% Utilisation = DIVIDE ( [Nb Utilisateurs], [Nb Repondants Question] )
```

* **Contrôle :** Python 86,7 % (2020), 87,6 % (2021), 88,3 % (2022).

### Outil N1
* **Objectif :** outil le plus utilisé de la sélection, hors « None » et « Other ».
* **Format :** texte.

```dax
Outil N1 =
// Outil le plus utilisé de la sélection, hors « None » et « Other »
VAR Candidats =
    FILTER ( ALLSELECTED ( Dim_Reponse[Option], Dim_Reponse[EstOutil] ), Dim_Reponse[EstOutil] = TRUE () )
VAR Premier = TOPN ( 1, Candidats, [Nb Utilisateurs], DESC )
RETURN
    CONCATENATEX ( Premier, Dim_Reponse[Option], ", " )
```

* **Contrôle (tableau thème × mesure) :** Cloud : Amazon Web Services (AWS) ; Environnements (IDE) : Jupyter ; Frameworks ML : Scikit-learn ; Langages : Python ; Outils BI : Tableau. Résultats identiques pour chaque édition prise séparément.

---

## Dossier Salaires

Toutes les valeurs sont en dollars (format Devise, symbole $ Anglais États-Unis, 0 décimale). Les salaires sont des estimations au milieu de tranche.

### Nb Avec Salaire
* **Format :** nombre entier, séparateur de milliers.

```dax
Nb Avec Salaire =
CALCULATE ( [Nb Repondants], Dim_Salaire[Communique] = TRUE () )
```

* **Contrôle :** 10 729 (2020), 15 391 (2021), 8 136 (2022).

### Taux Reponse Salaire
* **Format :** pourcentage, 1 décimale.

```dax
Taux Reponse Salaire = DIVIDE ( [Nb Avec Salaire], [Nb Repondants] )
```

* **Contrôle :** 53,6 % (2020), 59,3 % (2021), 33,9 % (2022).

### Salaire Moyen Estime

```dax
Salaire Moyen Estime =
// Les blancs (« Non communiqué ») sont ignorés par AVERAGEX
AVERAGEX ( Fact_Repondants, RELATED ( Dim_Salaire[MilieuUSD] ) )
```

* **Contrôle :** 44 949 $ (2020), 42 274 $ (2021), 54 433 $ (2022).

### Salaire Median Estime

```dax
Salaire Median Estime =
// MEDIANX compte les blancs comme 0 : on se limite aux répondants ayant déclaré un salaire
CALCULATE ( MEDIANX ( Fact_Repondants, RELATED ( Dim_Salaire[MilieuUSD] ) ), Dim_Salaire[Communique] = TRUE () )
```

* **Piège constaté :** sans le filtre sur `Communique`, la médiane affichait 500 $ en 2020 et 2021. Contrairement à `AVERAGEX`, `MEDIANX` compte les valeurs vides comme 0.
* **Contrôle :** 17 500 $ (2020), 12 500 $ (2021), 22 500 $ (2022).

### Salaire Moyen Fiable

```dax
Salaire Moyen Fiable =
// Pas de moyenne en dessous de 30 réponses
IF ( [Nb Avec Salaire] >= 30, [Salaire Moyen Estime] )
```

* **Contrôle :** identique à la moyenne estimée au niveau de l'année ; vide pour un pays dont `Nb Avec Salaire` est inférieur à 30.

---

## Mesures prévues

Elles seront ajoutées ici, avec leur formule et leur contrôle, au fur et à mesure de leur création.

* **Tendances :** `% Utilisation A-1` (part de l'édition précédente), `Evolution pts` (écart en points de pourcentage), `Rang Outil` (classement des outils).
* **Comparaison au monde :** `% Utilisation Monde`, `Ecart vs Monde pts` (fiche pays).
* **Profils :** `% Experimentes` (part des plus de 5 ans d'expérience), `% Repondants` (répartition par tranche de salaire).
