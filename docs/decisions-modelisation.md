# Phase 1.3 : Décisions de modélisation — Projet IA (Kaggle Survey)

Ce document récapitule les arbitrages et choix d'architecture retenus pour le modèle de données, conformément aux directives de la règle 0.4 du livre de recettes.

---

## 1. Choix du Schéma : Étoile Pur
* **Décision :** Implémentation d'un schéma en étoile strict.
* **Justification :** La table de fait (`Fact_Reponses`) est positionnée au centre, et les tables de dimensions (`Dim_Profil`, `Dim_Geographie`, `Dim_Outils`, `Dim_Date`) l'entourent directement.
* **Règle de floconnage :** Aucun schéma en flocon n'est appliqué. Les hiérarchies (comme le regroupement des pays par continent ou les outils par catégorie) sont dénormalisées directement au sein de leurs dimensions respectives pour maximiser les performances du moteur VertiPaq.

## 2. Configuration des Relations
* **Cardinalité :** Toutes les relations du modèle sont configurées en **Un-à-plusieurs (1:N)**, orientées depuis le côté "1" (les tables de dimensions) vers le côté "N" (la table de fait).
* **Sens du filtrage croisé :** Le sens du filtrage est strictement **Unidirectionnel**. 
* **Sécurité :** Les filtres bidirectionnels et les relations plusieurs-à-plusieurs sont formellement interdits afin d'éviter toute ambiguïté dans la propagation des filtres et garantir l'exactitude des calculs de parts de marché.

## 3. Optimisation des Clés et Performance
* **Types de clés :** Les jointures s'appuient exclusivement sur des clés de substitution de type **Entier** (`INT`), excluant toute clé textuelle.
* **Visibilité :** Toutes les clés techniques (`ID_...`) utilisées pour les relations sont systématiquement **masquées dans la vue Rapport** pour ne pas encombrer l'expérience utilisateur et forcer l'usage des attributs métiers.

## 4. Gestion de la Dimension Temporelle
* **Décision :** Création d'une table `Dim_Date` indépendante et continue couvrant l'intégralité des années des enquêtes (2020, 2021, 2022).
* **Marquage :** Cette table est explicitement désignée comme **Table de dates** (Mark as Date Table) dans Power BI Desktop pour assurer le fonctionnement natif et fiable des fonctions de Time Intelligence (calculs d'évolutions d'une année sur l'autre).
