# Sources de données - Baromètre Communauté Data et IA

## Origine
Kaggle Machine Learning & Data Science Survey, éditions 2020, 2021 et 2022 :
* 2020 : https://www.kaggle.com/competitions/kaggle-survey-2020
* 2021 : https://www.kaggle.com/competitions/kaggle-survey-2021
* 2022 : https://www.kaggle.com/competitions/kaggle-survey-2022

## Licence et conditions d'usage
Ces données sont celles de compétitions Kaggle. Selon les règles de la compétition (section sur l'accès et l'usage des données), elles peuvent être utilisées à des fins non commerciales, notamment pour l'éducation et la recherche académique. Ce projet est personnel, pédagogique et non commercial. Les fichiers bruts ne sont pas republiés dans ce dépôt : chaque personne les télécharge depuis Kaggle après avoir accepté les règles.

Règles de 2020, 2021 et 2022 relues le 08/11/2026.

## Téléchargement
1. Sur la page de chaque édition, cliquer sur « Join the competition » et accepter les règles.
2. Télécharger le fichier de réponses de chaque édition dans `data/raw/` (dossier exclu de Git par le `.gitignore`).
3. Date de téléchargement : novembre 2026.

| Fichier attendu | Répondants |
|---|---|
| `kaggle_survey_2020_responses.csv` | 20 036 |
| `kaggle_survey_2021_responses.csv` | 25 973 |
| `kaggle_survey_2022_responses.csv` | 23 997 |

## Tables de référence (`data/reference/`)
Fichiers créés pour ce projet et versionnés :

| Fichier | Rôle |
|---|---|
| `mapping_colonnes.csv` | Colonnes du profil, dont les numéros de question changent d'une année à l'autre |
| `mapping_themes.csv` | Préfixes des colonnes de chaque thème d'outils |
| `harmonisation_options.csv` | Libellés des outils regroupés (Jupyter, Qlik, orthographe) |
| `pays_region.csv` | Pays, région et continent |
| `tranches_salaire.csv` | Tranches de salaire, groupes et milieux de tranche |
| `harmonisation_profil.csv` | Libellés français et ordre de tri des modalités du profil |

## Notes techniques
* Les numéros de question et les noms de colonnes changent d'une édition à l'autre : l'harmonisation est faite dans Power Query à l'aide des tables de référence.
* Dans chaque fichier, la première ligne contient les codes des colonnes et la deuxième le texte des questions. Dans le fichier 2022, un libellé de question contient un retour à la ligne : ne pas ouvrir ni réenregistrer ces fichiers dans Excel.
* Aucun identifiant de répondant n'existe dans les fichiers sources : il est construit dans Power Query (année x 1 000 000 + numéro de ligne).