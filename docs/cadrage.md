# Phase 1.1 : Cadrage - Projet IA (Kaggle Survey)

Version mise à jour après validation de la maquette Figma et construction du modèle.

## 1. Contexte du projet
* **Dépôt :** `pbi-ia-kaggle-survey`
* **Dataset :** Kaggle Machine Learning & Data Science Survey, éditions 2020, 2021 et 2022 (70 006 répondants au total). Données utilisées à des fins pédagogiques et non commerciales, conformément aux règles des compétitions Kaggle.
* **Thème visuel :** sombre, violet et cyan.
* **Périmètre :** la communauté Kaggle, pas l'ensemble du marché data et IA.

## 2. Utilisateurs cibles
* Responsables formation.
* RH tech.
* Décideurs qui suivent les tendances data et IA.
* Data analyst présentant un baromètre d'évolution.

## 3. Matrice des questions métier
| # | Question métier | Page cible | Visuels principaux |
|---|---|---|---|
| Q1 | Combien de répondants et de pays, et comment évolue l'échantillon ? | Vue d'ensemble | Cartes KPI, « Répondants par édition » (3 colonnes), carte |
| Q2 | Quels langages, outils et frameworks progressent ou reculent d'une année sur l'autre ? | Outils et tendances | Barres classées, courbe du top 5, matrice avec évolution en points |
| Q3 | Quelle est la part de chaque outil selon le pays ? | Outils et tendances | Segments Année, Thème et Pays, barres classées |
| Q4 | Comment les salaires varient-ils selon le métier, le secteur, l'expérience et le pays ? | Salaires et profils | Paramètre de champs (Métier, Secteur, Expérience), colonnes, barres, nuage de points |
| Q5 | Où se situe un pays par rapport à la moyenne mondiale ? | Fiche pays (drillthrough) | Cartes, barres d'écart |

## 4. Indicateurs clés (KPI)
| KPI | Définition | Page |
|---|---|---|
| Nombre de répondants | Répondants de la sélection | Vue d'ensemble |
| Nombre de pays | Pays réels distincts (hors « Autre pays » et « Non communiqué ») | Vue d'ensemble |
| Outil n°1 | Outil le plus utilisé du thème choisi (hors « None » et « Other ») | Vue d'ensemble |
| Part d'utilisation | Utilisateurs de l'outil ÷ répondants à la question du thème | Outils et tendances |
| Évolution en points | Part de l'année moins part de l'édition précédente (en points de pourcentage) | Outils et tendances |
| Salaire moyen estimé | Moyenne des milieux de tranche, en dollars, sur les répondants ayant déclaré un salaire ; masquée sous 30 réponses | Vue d'ensemble, Salaires et profils |
| Taux de réponse à la question salaire | Répondants ayant déclaré un salaire ÷ répondants (affiché en note sous l'histogramme) | Salaires et profils |

## 5. Limites à connaître
* Les salaires sont des estimations (milieu de tranche), en dollars.
* Le secteur d'activité n'est posé qu'en 2021 et 2022.
* Certaines options n'existent que certaines années : une cellule vide n'est pas un 0 %.
* Le taux de réponse à la question salaire est de 54 % en 2020, 59 % en 2021 et 34 % en 2022 : les salaires 2022 sont à lire avec prudence.
* Les décisions de préparation et de modélisation sont détaillées dans `decisions-modelisation.md`.