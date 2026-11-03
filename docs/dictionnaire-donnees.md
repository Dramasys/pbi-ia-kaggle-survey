# Phase 1.2 : Dictionnaire de données — Projet IA (Kaggle Survey)

Ce document répertorie la structure logique des tables du modèle en étoile, conformément aux règles de modélisation (0.4). Les clés de substitution sont des entiers et sont masquées dans la vue Rapport.

---

## 1. Table de Fait : Fact_Reponses
Contient l'ensemble des réponses individuelles à l'enquête Kaggle (éditions 2020, 2021, 2022).

| Nom de la colonne | Type de données | Description / Rôle | Règle métier |
|---|---|---|---|
| ID_Reponse | Entier | Clé primaire de la ligne (Clé de substitution) | Masquée |
| ID_Date | Entier | Clé étrangère vers Dim_Date (Année de l'enquête) | Masquée |
| ID_Profil | Entier | Clé étrangère vers Dim_Profil | Masquée |
| ID_Geographie | Entier | Clé étrangère vers Dim_Geographie | Masquée |
| ID_Outil | Entier | Clé étrangère vers Dim_Outils | Masquée |
| Salaire_Estime | Décimal | Salaire annuel converti en USD | KPI : Salaire moyen estimé |
| Flag_Reponse_Salaire | Booléen | Indique si le répondant a renseigné son salaire | KPI : Taux de réponse salaire |

---

## 2. Tables de Dimensions

### Dim_Profil
Regroupe les caractéristiques socioprofessionnelles et l'expérience des répondants.

| Nom de la colonne | Type de données | Description |
|---|---|---|
| ID_Profil | Entier | Clé primaire (Masquée) |
| Metier | Texte | Intitulé du poste (ex: Data Scientist, ML Engineer) |
| Experience_Codage | Texte | Tranche d'années d'expérience en programmation |
| Niveau_Etudes | Texte | Plus haut diplôme obtenu |

### Dim_Geographie
Permet l'analyse par zone et la comparaison avec la moyenne mondiale (Page Fiche Pays).

| Nom de la colonne | Type de données | Description |
|---|---|---|
| ID_Geographie | Entier | Clé primaire (Masquée) |
| Pays | Texte | Nom du pays du répondant |
| Continent | Texte | Regroupement géographique majeur |

### Dim_Outils
Contient les langages, frameworks et technologies suivis d'une année sur l'autre.

| Nom de la colonne | Type de données | Description |
|---|---|---|
| ID_Outil | Entier | Clé primaire (Masquée) |
| Categorie_Outil | Texte | Type de technologie (Language, Cloud, Framework) |
| Nom_Outil | Texte | Nom de l'outil (ex: Python, R, AWS, TensorFlow) |

### Dim_Date
Table de dates dédiée, couvrant les années pleines des enquêtes (2020 à 2022), marquée comme table de dates dans Power BI.

| Nom de la colonne | Type de données | Description |
|---|---|---|
| ID_Date | Entier | Clé primaire (Format AAAAMMJJ) (Masquée) |
| Date | Date | Date complète |
| Annee_Enquete | Entier | Année de l'édition (2020, 2021, 2022) |
