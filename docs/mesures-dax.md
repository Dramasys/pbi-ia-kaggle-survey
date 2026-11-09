# Registre des mesures DAX - Projet IA (Kaggle Survey)

Toutes les mesures sont dans la table `_Mesures`. Conventions : `DIVIDE` pour toute division, `VAR ... RETURN` quand une expression est longue ou répétée, commentaire `//` placé **après** la première ligne (le nom de la mesure doit être en tête dans la barre de formule). Les colonnes ne sont jamais totalisées (« Totaliser par = Aucun ») : tous les calculs passent par des mesures.

## Avancement

| Mesure | Dossier | Statut |
|---|---|---|
| Nb Repondants, Nb Utilisateurs, Nb Repondants Question, Nb Pays | Volumes | Implémentée |
| % Utilisation, Outil N1 | Parts | Implémentée |
| Nb Avec Salaire, Taux Reponse Salaire | Salaires | Implémentée |
| Salaire Moyen Estime, Salaire Median Estime, Salaire Moyen Fiable | Salaires | Implémentée |
| % Utilisation A-1, Evolution pts, Rang Outil | Tendances | Implémentée |
| Part du Pays, % Utilisation Monde, Ecart vs Monde pts, % Experimentes | Parts | Implémentée |
| % Repondants Salaire | Salaires | Implémentée |

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
// Base valable seulement si un seul thème est sélectionné ; l'outil est retiré du filtre
IF (
    HASONEVALUE ( Dim_Reponse[Theme] ),
    CALCULATE ( [Nb Utilisateurs], REMOVEFILTERS ( Dim_Reponse[Option] ) )
)
```

* **Protection :** la mesure est vide si aucun thème, ou plusieurs thèmes, sont sélectionnés (un segment en sélection unique permet de désélectionner la valeur). `% Utilisation`, `Evolution pts` et les mesures qui en dépendent sont alors vides au lieu d'afficher des valeurs sans signification.

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

### Part du Pays
* **Objectif :** poids du pays sélectionné dans l'ensemble des répondants (fiche pays).
* **Format :** pourcentage, 1 décimale.

```dax
Part du Pays =
// Poids du pays dans l'ensemble des répondants : le filtre pays est retiré du dénominateur
DIVIDE ( [Nb Repondants], CALCULATE ( [Nb Repondants], REMOVEFILTERS ( Dim_Pays ) ) )
```

* **Contrôle (France) :** 1,6 % (2020), 1,5 % (2021), 1,1 % (2022).

### % Utilisation Monde
* **Objectif :** part d'utilisation mondiale, identique pour tous les pays (référence de comparaison).
* **Format :** pourcentage, 1 décimale.

```dax
% Utilisation Monde =
// Le filtre pays est retiré ; thème, outil et année sont conservés
CALCULATE ( [% Utilisation], REMOVEFILTERS ( Dim_Pays ) )
```

* **Contrôle (Langages, Python, sans filtre pays) :** égale à `% Utilisation`, soit 86,7 %, 87,6 % et 88,3 %. Avec un pays sélectionné, la valeur ne change pas.

### Ecart vs Monde pts
* **Objectif :** écart, en points de pourcentage, entre la part d'utilisation du pays et la part mondiale.
* **Format :** personnalisé `+0.0" pts";-0.0" pts";0.0" pts"`.

```dax
Ecart vs Monde pts =
// Vide si la part du pays ou la part mondiale n'existe pas
VAR Monde = [% Utilisation Monde]
RETURN
    IF ( ISBLANK ( [% Utilisation] ) || ISBLANK ( Monde ), BLANK (), ( [% Utilisation] - Monde ) * 100 )
```

* **Contrôle :** 0,0 pts sans filtre pays ; avec un pays, l'écart vaut (part du pays - part mondiale) × 100.

### % Experimentes
* **Objectif :** part des répondants ayant plus de 5 ans de pratique du code, parmi ceux qui ont répondu à la question.
* **Format :** pourcentage, 1 décimale.

```dax
% Experimentes =
// Le dénominateur exclut « Non répondu »
DIVIDE (
    CALCULATE ( [Nb Repondants], Dim_Experience[ExperienceGroupe] = "Plus de 5 ans" ),
    CALCULATE ( [Nb Repondants], Dim_Experience[ExperienceGroupe] <> "Non répondu" )
)
```

* **Contrôle :** 29,5 % (2020), 27,4 % (2021), 25,4 % (2022).

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

### % Repondants Salaire
* **Objectif :** répartition des répondants ayant déclaré un salaire, par tranche ou par groupe (histogramme de la page « Salaires et profils »).
* **Format :** pourcentage, 1 décimale.

```dax
% Repondants Salaire =
// Base : répondants ayant déclaré un salaire (la tranche est retirée du dénominateur)
DIVIDE (
    [Nb Repondants],
    CALCULATE ( [Nb Repondants], REMOVEFILTERS ( Dim_Salaire ), Dim_Salaire[Communique] = TRUE () )
)
```

* **Usage :** exclure le groupe « Non communiqué » dans le visuel (sa valeur n'a pas de sens).
* **Contrôle (par groupe, 2020 / 2021 / 2022) :** « < 10 k$ » 41,4 / 45,5 / 37,6 % ; « 10-30 k$ » 17,0 / 16,8 / 17,3 % ; « 30-60 k$ » 14,9 / 13,8 / 15,4 % ; « 60-100 k$ » 12,6 / 11,4 / 12,6 % ; « 100-150 k$ » 8,3 / 7,2 / 8,3 % ; « 150-250 k$ » 4,3 / 3,7 / 6,1 % ; « > 250 k$ » 1,4 / 1,7 / 2,8 %.
* **Lecture :** la distribution est très asymétrique, avec 38 à 46 % des répondants sous 10 000 $ par an.

---

## Dossier Tendances

Ces mesures s'utilisent avec `Dim_Annee[Edition]` en colonnes ou en axe. Dans une matrice, désactiver les totaux : ils mélangent les trois éditions.

### % Utilisation A-1
* **Objectif :** part de l'édition précédente.
* **Format :** pourcentage, 1 décimale.

```dax
% Utilisation A-1 =
// Part de l'édition précédente : année courante moins un, filtre d'année retiré
VAR AnneeCourante = MAX ( Dim_Annee[Annee] )
RETURN
    CALCULATE ( [% Utilisation], REMOVEFILTERS ( Dim_Annee ), Dim_Annee[Annee] = AnneeCourante - 1 )
```

* **Piège :** si plusieurs éditions sont sélectionnées, c'est la plus récente qui sert de référence.

### Evolution pts
* **Objectif :** écart en points de pourcentage avec l'édition précédente.
* **Format :** personnalisé `+0.0" pts";-0.0" pts";0.0" pts"`.

```dax
Evolution pts =
// Écart en points de pourcentage ; vide si l'option n'existait pas l'an dernier ou n'existe plus cette année
VAR Precedent = [% Utilisation A-1]
RETURN
    IF ( ISBLANK ( [% Utilisation] ) || ISBLANK ( Precedent ), BLANK (), ( [% Utilisation] - Precedent ) * 100 )
```

* **Contrôle (Langages) :** Python vide (2020), +0,9 pts (2021), +0,6 pts (2022) ; Swift -0,1 pts en 2021, vide en 2020 et 2022 ; C#, Go et PHP vides en 2020, 2021 et 2022 (pas d'année précédente en 2022). « None » et « Other » ont une évolution (vide en 2020) mais pas de rang.

### Rang Outil
* **Objectif :** classement des outils de la sélection (1 = le plus utilisé).
* **Format :** nombre entier.

```dax
Rang Outil =
// Vide pour « None », « Other » et pour une option sans utilisateur dans la sélection
VAR Candidats =
    FILTER ( ALLSELECTED ( Dim_Reponse[Option], Dim_Reponse[EstOutil] ), Dim_Reponse[EstOutil] = TRUE () )
RETURN
    IF (
        SELECTEDVALUE ( Dim_Reponse[EstOutil] ) = TRUE () && NOT ISBLANK ( [Nb Utilisateurs] ),
        RANKX ( Candidats, [Nb Utilisateurs], , DESC, Dense )
    )
```

* **Piège constaté :** dans la première version, une option non proposée une année recevait le dernier rang (Swift avait le rang 14 en 2022, alors qu'elle n'y existe pas). Le test `NOT ISBLANK ( [Nb Utilisateurs] )` corrige ce comportement.
* **Contrôle (Langages) :** 2020 et 2021 : rangs de 1 à 11 ; 2022 : rangs de 1 à 13 (Python, SQL, R, C++, Java, C, JavaScript, MATLAB, Bash, C#, PHP, Go, Julia). R : 3, 4, 3 ; C++ : 4, 3, 4 ; Swift : 11, 11, vide ; C#, Go et PHP vides en 2020 et 2021 ; « None » et « Other » vides.

---

## Mesures prévues

Aucune mesure n'est prévue à ce stade. Des mesures pourront être ajoutées pendant la construction des pages (phase 1.7) : elles seront documentées ici avec leur formule et leur contrôle.
