# Baromètre Communauté Data et IA - Projet Power BI

Projet personnel à but pédagogique : un rapport Power BI construit à partir du Kaggle Machine Learning & Data Science Survey (éditions 2020, 2021 et 2022, soit 70 006 répondants). Il suit un parcours complet : cadrage, maquette Figma, préparation des données, modèle en étoile, mesures DAX, rapport, sécurité (RLS) et documentation.

## Utilisateurs et questions métier
Responsables formation, RH tech, décideurs qui suivent les tendances data et IA, data analysts. Les questions auxquelles le rapport répond et les KPI sont décrits dans [docs/cadrage.md](docs/cadrage.md).

## Données
Source : Kaggle Machine Learning & Data Science Survey. Les données sont utilisées à des fins non commerciales (éducation), conformément aux règles des compétitions Kaggle. Les fichiers bruts ne sont pas publiés dans ce dépôt. Détails, liens et licence : [data/README.md](data/README.md).

## Modèle
Schéma en étoile à deux tables de faits (`Fact_Repondants` : 70 006 lignes ; `Fact_Usage_Outils` : 533 319 lignes), sept dimensions et des relations un-à-plusieurs unidirectionnelles. Les décisions de modélisation et de préparation sont dans [docs/decisions-modelisation.md](docs/decisions-modelisation.md), les tables et colonnes dans [docs/dictionnaire-donnees.md](docs/dictionnaire-donnees.md), les mesures dans [docs/mesures-dax.md](docs/mesures-dax.md).

## Avancement
| Étape | Statut |
|---|---|
| Cadrage | Terminé |
| Dépôt et données | Terminé |
| Maquette Figma (v2) | Terminé |
| Préparation des données (Power Query) | Terminé |
| Modèle en étoile | Terminé |
| Mesures DAX | Terminé |
| Pages du rapport | Terminé |
| Sécurité (RLS) | À faire |
| Performance et accessibilité | À faire |
| Documentation PDF et livraison | À faire |

## Ouvrir le projet
1. Cloner le dépôt et installer Power BI Desktop (projet créé avec la version 2.159, octobre 2026).
2. Télécharger les trois fichiers de données Kaggle dans `data/raw/` en suivant [data/README.md](data/README.md).
3. Ouvrir `powerbi/ia-kaggle-survey.pbip`.
4. Dans **Transformer les données > Gérer les paramètres**, remplacer les deux chemins par ceux de votre machine (avec l'antislash final) : `pDossierData` (dossier `data/raw/`) et `pDossierRef` (dossier `data/reference/`).
5. Actualiser le modèle.

## Structure du dépôt
```
data/            README, raw/ (non versionné), reference/ (tables de référence)
docs/            cadrage, décisions de modélisation, dictionnaire, mesures DAX, guide utilisateur
figma/           exports PNG des maquettes
powerbi/         projet PBIP (rapport et modèle sémantique)
screenshots/     captures (à venir)
theme/           thème du rapport (theme.json)
```

## Charte graphique et maquette Figma

### Canevas et grille
* Format du cadre : 1280 x 720 px (ratio 16:9).
* Grille : 12 colonnes, marges de 24 px, gouttières de 16 px.
* Composants clés (Auto layout) :
  * Bandeau d'en-tête : hauteur 64 px.
  * Barre de filtres supérieure : hauteur 48 px.
  * Cartes KPI : largeur 278 px, hauteur 96 px, rayon de courbure 8.

### Hiérarchie typographique (police Segoe UI)
* Titre de page : taille 20, semibold.
* Titre de visuel : taille 14, semibold.
* Valeur KPI : taille 32, bold.
* Libellé et axe : taille 11, regular.
* Note et source : taille 10, regular (non présente dans les maquettes).

### Organisation du fichier Figma
Le fichier contient 6 cadres : 5 pages de rapport de 1280 x 720 px et une info-bulle de 300 x 200 px.

1. Page 1 : Vue d'ensemble
2. Page 2 : Outils et tendances
3. Page 3 : Salaires & Profils
4. Page 4 : Guide
5. Page 5 : Fiche pays (drillthrough)
6. Tooltip Rapport (info-bulle)

Les exports PNG (2x) servent de référence pour la construction du rapport Power BI.

### Statut
Maquette validée (v2). Les valeurs affichées sont fictives : elles servent à valider la mise en page, pas à représenter les données réelles.

### Liens Figma (lecture seule)
* [Fichier complet](https://www.figma.com/design/XJDgt8PlIplhG0d4FcCzAv/Maquette-pbi-ia-kaggle-survey?node-id=0-1&t=ZDFPMw9Uc35yaFPp-1)
* [Dashboard principal](https://www.figma.com/design/XJDgt8PlIplhG0d4FcCzAv/Maquette-pbi-ia-kaggle-survey?node-id=0-1) : Vue d'ensemble, Outils et tendances, Salaires et profils
* [Guide et détails](https://www.figma.com/design/XJDgt8PlIplhG0d4FcCzAv/Maquette-pbi-ia-kaggle-survey?node-id=20-450) : Guide, Fiche pays, info-bulle
