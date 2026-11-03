# Phase 1.4 : Registre des mesures DAX — Projet IA (Kaggle Survey)

Toutes les mesures listées ci-dessous doivent être stockées dans la table unique `_Mesures`. Elles utilisent exclusivement l'architecture `VAR ... RETURN` et la fonction `DIVIDE` pour sécuriser les calculs.

---

## Dossier d'affichage : Volumes

### [Nombre de répondants]
* **Objectif métier :** Compter le nombre total de professionnels ayant validé le questionnaire.
* **Formule DAX :**
```dax
Nombre de répondants = 
VAR TotalReponses = COUNTROWS('Fact_Reponses')
RETURN
    TotalReponses
```

### [Nombre de pays]
* **Objectif métier :** Identifier l'étendue géographique de l'échantillon annuel.
* **Formule DAX :**
```dax
Nombre de pays = 
VAR TotalPays = DISTINCTCOUNT('Dim_Geographie'[Pays])
RETURN
    TotalPays
```

---

## Dossier d'affichage : Taux et Parts

### [Part d'utilisation d'un outil]
* **Objectif métier :** Calculer la pénétration d'une technologie par rapport à l'ensemble des réponses de la sélection courante.
* **Formule DAX :**
```dax
Part d'utilisation d'un outil = 
// Calcule le volume de l'outil sélectionné vs l'ensemble des outils
VAR ReponsesOutil = [Nombre de répondants]
VAR ReponsesToutesTechnologies = CALCULATE([Nombre de répondants], ALL('Dim_Outils'))
VAR Resultat = DIVIDE(ReponsesOutil, ReponsesToutesTechnologies, 0)
RETURN
    Resultat
```

### [Évolution en points de pourcentage]
* **Objectif métier :** Mesurer la progression ou le recul d'adoption d'un outil d'une année sur l'autre (Ex: +2,5 pts).
* **Formule DAX :**
```dax
Évolution en points de pourcentage = 
// Calcule l'écart strict entre la part de l'année en cours et celle de l'année précédente
VAR AnneeCourante = SELECTEDVALUE('Dim_Date'[Annee_Enquete])
VAR PartAnneeEnCours = [Part d'utilisation d'un outil]
VAR PartAnneePrecedente = 
    CALCULATE(
        [Part d'utilisation d'un outil],
        'Dim_Date'[Annee_Enquete] = AnneeCourante - 1
    )
VAR EcartPoints = PartAnneeEnCours - PartAnneePrecedente
RETURN
    IF(ISBLANK(PartAnneePrecedente), BLANK(), EcartPoints)
```

### [Taux de réponse à la question salaire]
* **Objectif métier :** Suivre la représentativité et la fiabilité de nos analyses salariales.
* **Formule DAX :**
```dax
Taux de réponse à la question salaire = 
// Évalue la part des répondants ayant accepté de divulguer leurs revenus
VAR ReponsesAvecSalaire = CALCULATE([Nombre de répondants], 'Fact_Reponses'[Flag_Reponse_Salaire] = TRUE)
VAR DonneesTotales = [Nombre de répondants]
VAR Ratio = DIVIDE(ReponsesAvecSalaire, DonneesTotales, 0)
RETURN
    Ratio
```

---

## Dossier d'affichage : Rémunérations

### [Salaire moyen estimé]
* **Objectif métier :** Établir le baromètre de rémunération globale convertible selon les axes profils et pays.
* **Formule DAX :**
```dax
Salaire moyen estimé = 
// Exclut les blancs pour ne pas fausser la moyenne du baromètre RH
VAR SommeSalaires = SUM('Fact_Reponses'[Salaire_Estime])
VAR ReponsesValides = CALCULATE([Nombre de répondants], 'Fact_Reponses'[Flag_Reponse_Salaire] = TRUE)
VAR Moyenne = DIVIDE(SommeSalaires, ReponsesValides, 0)
RETURN
    Moyenne
```
