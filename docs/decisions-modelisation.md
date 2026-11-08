# Décisions de modélisation et de préparation - Projet IA (Kaggle Survey)

Ce document récapitule les arbitrages retenus pour la préparation des données et le modèle. État : préparation des données et modèle en étoile implémentés, mesures en cours.

---

## 1. Schéma en étoile à deux tables de faits
* **Décision :** deux tables de faits liées à des dimensions conformes.
  * `Fact_Repondants` : une ligne par répondant (sert de dénominateur : base des parts et des moyennes).
  * `Fact_Usage_Outils` : une ligne par répondant et par option cochée (sert de numérateur : utilisateurs d'un outil).
* **Dimensions :** `Dim_Annee`, `Dim_Pays` (hiérarchie Continent > Région > Pays), `Dim_Demographie` (âge, genre, études), `Dim_Emploi` (métier, secteur), `Dim_Experience` (expérience de codage et de ML), `Dim_Salaire` (tranche, groupe, ordre, milieu de tranche en USD) et `Dim_Reponse` (thème, option, indicateur « est un outil »), cette dernière reliée uniquement à `Fact_Usage_Outils`.
* **Pas de dimension « junk » unique pour le profil :** regroupées, les sept colonnes donnent 31 182 combinaisons pour 70 006 répondants (45 %), sans réduction utile. Trois dimensions de 350, 299 et 72 lignes (721 au total) sont retenues.
* **Justification des deux faits :** une part d'utilisation = utilisateurs de l'outil ÷ répondants à la question. Les deux comptes ne se trouvent pas dans la même table.
* **Pas de relation directe entre les deux faits :** on les compare via les dimensions partagées.
* **Règle de floconnage :** aucun flocon. Les hiérarchies sont dénormalisées dans leur dimension.

## 2. Configuration des relations
* **Cardinalité :** toutes les relations sont en **un-à-plusieurs**, de la dimension (côté 1) vers le fait (côté plusieurs).
* **Filtrage :** strictement **unidirectionnel**.
* **Interdits :** filtres bidirectionnels et relations plusieurs-à-plusieurs, sauf justification documentée ici.
* **Implémentation :** 13 relations (6 depuis `Fact_Repondants`, 7 depuis `Fact_Usage_Outils`), toutes plusieurs-à-un vers la dimension, filtre unidirectionnel, aucune relation entre les deux faits. Les relations créées automatiquement au chargement ont été revues : une relation inactive entre les deux faits a été supprimée et la relation `AnneeKey` du fait d'usage a été ajoutée.

## 3. Clés et performance
* **Clés :** entiers (`PaysKey`, `DemographieKey`, `EmploiKey`, `ExperienceKey`, `SalaireKey`, `ReponseKey`, `Annee`). Aucune clé textuelle.
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
* La distribution réelle des salaires est fortement décalée vers les petites tranches (38 à 46 % des répondants sous 10 000 $), contrairement à la forme en cloche de la maquette.
* Le segment « Secteur » n'a pas de données en 2020.
* Les libellés d'études, de métiers et les tranches de salaire de la maquette seront remplacés par ceux de l'enquête.
* Les salaires sont des estimations en dollars (milieu de tranche), pas en euros.
* La page 1 affichera « Répondants par édition » en trois colonnes, et non une courbe d'inscriptions cumulées.
* La matrice de la page 2 aura des cellules vides pour les outils non proposés certaines années (C#, Go et PHP en 2020 et 2021).
* Le métier « Data Architect » n'existe qu'en 2022, et « Consultant Data » n'est pas un choix de l'enquête.
* La barre cyan fine qui marque la page active dans la maquette n'est pas reproductible dans Power BI (taille minimale d'un élément : 12 px). La page active est signalée par un bouton en gras, de couleur cyan, sur fond plus clair.

## 7. Tables de référence et regroupements (`data/reference/`)
Six fichiers, créés à la main et versionnés, pilotent l'harmonisation des trois éditions :
* `mapping_colonnes.csv` : colonnes du profil (le numéro de question change d'une année à l'autre).
* `mapping_themes.csv` : préfixes des colonnes de chaque thème d'outils.
* `harmonisation_options.csv` : libellés des outils (Jupyter, Qlik, orthographe) et indicateur `EstOutil`.
* `pays_region.csv` : 69 libellés source (67 pays réels et 2 libellés « Autre pays » et « Non communiqué »). Après regroupement de la Corée du Sud, la dimension compte 66 pays réels + 2 = 68 lignes. Chaque pays a une région (12 régions réelles + « Non précisé ») et un continent (5 continents + « Non précisé »). Noms français pour l'affichage (`Pays`) et anglais pour la carte (`PaysEN`).
* `tranches_salaire.csv` : 25 tranches harmonisées, 7 groupes pour les graphiques.
* `harmonisation_profil.csv` : valeurs françaises, ordre de tri et groupes (âge, genre, études, expérience, métier, secteur).

Décisions :
* **Absence de réponse** : marqueur `(vide)` dans les fichiers de référence. Les cellules vides sont remplacées par ce marqueur dans Power Query avant la jointure.
* **Corée du Sud** : « Republic of Korea » (76 réponses en 2020) et « South Korea » regroupés (doublon d'option en 2020).
* **Diplôme professionnel** : « Professional degree » (2020 seul) et « Professional doctorate » (2021 et 2022 seuls) regroupés, car ils ne coexistent jamais. Hypothèse : même option renommée.
* **Métiers** : Data Analyst, ML Engineer et Developer Advocate regroupés (renommages). Product Manager, Product/Project Manager, Program/Project Manager et Manager regroupés en « Manager (produit, projet, programme) » : regroupement approximatif, le libellé 2022 inclut aussi les directions.
* **Expérience de codage** : « 1-2 ans » (2020) et « 1-3 ans » (2021 et 2022) restent séparés (tranches non équivalentes). Les tranches de plus de 5 ans sont stables et forment le groupe « Plus de 5 ans ».
* **Salaires** : milieu de tranche = (borne basse + borne haute + 1) ÷ 2. La tranche ouverte « 500 000 et plus » (trois libellés selon l'année) est fixée à 600 000 $ : hypothèse arbitraire, peu influente sur la médiane.
* **Étudiants 2022** : le métier est vide pour 13 367 répondants en 2022, car la question « étudiant » est posée séparément (`Q5`). Les 11 961 répondants déclarés étudiants et sans métier sont reclassés en « Student ». Restent 1 406 métiers vides (non-réponses réelles).
