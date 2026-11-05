# Décisions de modélisation et de préparation - Projet IA (Kaggle Survey)

Ce document récapitule les arbitrages retenus pour la préparation des données et le modèle. Les éléments marqués « prévu » seront implémentés dans les étapes suivantes.

---

## 1. Schéma en étoile à deux tables de faits (prévu)
* **Décision :** deux tables de faits liées à des dimensions conformes.
  * `Fact_Repondants` : une ligne par répondant (sert de dénominateur : base des parts et des moyennes).
  * `Fact_Usage_Outils` : une ligne par répondant et par option cochée (sert de numérateur : utilisateurs d'un outil).
* **Dimensions :** `Dim_Annee`, `Dim_Pays` (hiérarchie Continent > Région > Pays), `Dim_Profil` (dimension « junk » : âge, genre, études, métier, expérience, secteur), `Dim_Salaire` (tranche, ordre, milieu de tranche en USD) et `Dim_Reponse` (thème, option, indicateur « est un outil »), cette dernière reliée uniquement à `Fact_Usage_Outils`.
* **Justification des deux faits :** une part d'utilisation = utilisateurs de l'outil ÷ répondants à la question. Les deux comptes ne se trouvent pas dans la même table.
* **Pas de relation directe entre les deux faits :** on les compare via les dimensions partagées.
* **Règle de floconnage :** aucun flocon. Les hiérarchies sont dénormalisées dans leur dimension.

## 2. Configuration des relations (prévu)
* **Cardinalité :** toutes les relations sont en **un-à-plusieurs**, de la dimension (côté 1) vers le fait (côté plusieurs).
* **Filtrage :** strictement **unidirectionnel**.
* **Interdits :** filtres bidirectionnels et relations plusieurs-à-plusieurs, sauf justification documentée ici.

## 3. Clés et performance
* **Clés :** entiers (`PaysKey`, `ProfilKey`, `SalaireKey`, `ReponseKey`, `Annee`). Aucune clé textuelle.
* **Identifiant de répondant :** absent de la source. `RepondantID` = année × 1 000 000 + numéro de ligne (unique sur les trois éditions). Il reste dans les faits comme dimension dégénérée, pour compter les répondants distincts.
* **Visibilité :** toutes les clés et `RepondantID` sont masquées dans la vue Rapport.

## 4. Absence de table de dates
* **Décision :** pas de `Dim_Date`. L'enquête est annuelle et ne contient aucune date.
* **Conséquence :** une dimension `Dim_Annee` (2020, 2021, 2022). L'évolution d'une année à l'autre se calcule par l'année courante moins un (`REMOVEFILTERS` sur `Dim_Annee`), pas avec les fonctions de time intelligence. Pas de marquage « table de dates ».
* **Évolution du projet :** si des données datées sont ajoutées, créer une table de dates dédiée.

## 5. Préparation des données (Power Query)
* **Effectifs :** 20 036 répondants en 2020, 25 973 en 2021, 23 997 en 2022, soit 70 006 au total.
* **Schéma variable :** les numéros de question changent d'une année à l'autre. Deux tables de référence les harmonisent : `mapping_colonnes.csv` (profil) et `mapping_themes.csv` (préfixes des colonnes par thème).
* **Colonnes « A » et « B » :** en 2020 et 2021, certaines questions ont deux séries (outils utilisés / outils que l'on veut apprendre). Les préfixes `Q26_A_`, `Q27_A_`, `Q31_A_` et `Q34_A_` ne retiennent que les outils utilisés.
* **Secteur d'activité :** question absente en 2020 ; valeur « Non posée » pour cette année.
* **Texte parasite « nan » :** le fichier 2022 contient une option « nan » (61 668 lignes dans le thème IDE) : exclue de l'unpivot.
* **Apostrophes :** l'apostrophe typographique est remplacée par l'apostrophe droite, pour éviter qu'un même libellé soit compté deux fois.
* **Harmonisation des options** (`harmonisation_options.csv`) :
  * Jupyter (2020), Jupyter Notebook (2021, 2022) et JupyterLab (2022) regroupés en **Jupyter**, avec un seul comptage par répondant (7 469 doublons supprimés : 540 788 lignes avant, 533 319 après).
  * Qlik et Qlik Sense regroupés en **Qlik**.
  * Orthographe : JavaScript, XGBoost, Hugging Face.
  * « None » et « Other » conservés avec l'indicateur `EstOutil = Non`, pour les exclure des classements.
* **Non regroupés :** Einstein Analytics (2020) et Tableau CRM (2021) restent séparés, faute d'équivalence vérifiée.
* **Options non proposées une année :** C#, Go, PHP et Huawei Cloud existent seulement en 2022 ; Swift, H2O 3, MXNet, Prophet et Salesforce disparaissent en 2022. Ces cellules restent vides : une option non proposée n'est pas à 0 %.

## 6. Écarts connus entre la maquette Figma et le rendu final
* Les valeurs de la maquette sont fictives (effectifs, pourcentages, salaires, pays).
* Le segment « Secteur » n'a pas de données en 2020.
* Les libellés d'études, de métiers et les tranches de salaire de la maquette seront remplacés par ceux de l'enquête.
* Les salaires sont des estimations en dollars (milieu de tranche), pas en euros.
