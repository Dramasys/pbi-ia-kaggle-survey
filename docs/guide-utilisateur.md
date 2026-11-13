# Guide utilisateur - Baromètre Communauté Data et IA

## À quoi sert ce tableau de bord
Il présente les réponses à l'enquête annuelle de Kaggle sur la data et le machine learning (éditions 2020, 2021 et 2022, soit 70 006 répondants) : qui répond, quels outils sont utilisés, comment ils évoluent et quels salaires sont déclarés. Il reflète la communauté Kaggle, pas l'ensemble du marché.

Il s'adresse aux responsables formation, aux RH tech, aux décideurs qui suivent les tendances data et IA, et aux data analysts.

## Naviguer
Le menu de gauche donne accès à trois pages : **Accueil** (Vue d'ensemble), **Outils** (Outils et tendances) et **Salaires** (Salaires et profils). La page **Guide** (en bas du menu) résume ce document. La page active est en gras, en cyan, sur fond plus clair.

## Les pages

### Vue d'ensemble
* Quatre indicateurs : nombre de répondants, nombre de pays, outil n° 1 (langage de programmation le plus utilisé) et salaire moyen estimé.
* Le **sélecteur d'année** (2020, 2021, 2022) en haut à droite met à jour les indicateurs, la carte, le classement des langages et les niveaux d'études.
* Le graphique « Répondants par édition » montre toujours les trois années.
* **Fiche pays** : clic droit sur une bulle de la carte, puis **Extraire > Fiche pays**.

### Outils et tendances
* Trois listes déroulantes : **Année**, **Thème** (langages, environnements de développement, frameworks de machine learning, cloud, outils de BI) et **Pays**.
* Choisissez toujours **un seul thème** : un pourcentage n'a de sens que pour un thème à la fois. Les listes Année et Thème gardent toujours une valeur choisie.
* Les barres montrent le pourcentage d'utilisation de chaque outil pour l'année choisie. La courbe et le tableau comparent les trois éditions (ils ne dépendent pas de la liste Année).
* Dans le tableau, la colonne « Évol. pts » donne l'écart avec l'édition précédente, en points de pourcentage.

### Salaires et profils
* Les trois boutons **Métier**, **Secteur** et **Expérience** changent l'axe du graphique « Salaire moyen estimé ».
* L'histogramme montre la répartition des salaires déclarés par tranche, et le nuage de points compare les 15 pays les plus représentés (salaire moyen et part des plus de 5 ans d'expérience).
* Cette page regroupe les trois éditions.

### Fiche pays
Elle s'ouvre par extraction depuis la carte de la page « Vue d'ensemble » ou depuis le nuage de points de la page « Salaires et profils ». Elle regroupe les trois éditions : répondants, part du pays, salaire moyen estimé, part des plus de 5 ans d'expérience, écart des langages avec le monde, niveaux d'études et métiers. Le bouton **Retour** ramène à la page précédente.

### Info-bulles
Survolez une barre du classement des langages (page « Vue d'ensemble ») ou des barres d'utilisation (page « Outils et tendances ») : l'info-bulle affiche l'outil, l'édition, la part d'utilisation, son évolution et le nombre de répondants à la question.

## Lire les chiffres
* **Part d'utilisation** : pourcentage des répondants à la question du thème qui utilisent l'outil.
* **Évolution en points** : « +2,3 pts » signifie une hausse de 2,3 points de pourcentage par rapport à l'édition précédente. Elle est vide pour 2020 (pas d'édition précédente) et quand l'outil n'était pas proposé l'une des deux années.
* **Salaires** : estimations en dollars au milieu de la tranche déclarée. Seuls les répondants ayant indiqué un salaire sont comptés. Aucune moyenne n'est affichée en dessous de 30 réponses.
* **Secteur** : la question n'a été posée qu'en 2021 et 2022. « Non posée » correspond à l'édition 2020.
* **Couleurs** : violet pour les effectifs, cyan pour les parts et les valeurs calculées ; le vert et le rouge signalent une hausse ou une baisse, toujours accompagnées d'un signe + ou −.

## Questions fréquentes
**Pourquoi une cellule est vide ?** L'outil n'était pas proposé cette année-là dans le questionnaire : une cellule vide n'est pas un 0 %.

**Pourquoi la part d'un pays diffère-t-elle de la part mondiale ?** C'est le sens de la comparaison : la fiche pays mesure l'écart entre le pays et l'ensemble des répondants.

**Pourquoi aucune moyenne pour certains pays ?** Moins de 30 répondants du pays ont indiqué leur salaire.

**Les graphiques sont vides après un choix de filtre. Que faire ?** Vérifiez qu'un thème est choisi sur la page « Outils et tendances » et que le pays choisi a des réponses pour l'année affichée.

## Profils d'accès
Les utilisateurs d'un rôle régional ne voient que les répondants des pays de leur zone, et les comparaisons « monde » portent alors sur cette zone. Le détail est dans [securite-rls.md](securite-rls.md).

## Limites
Les données viennent d'une enquête volontaire auprès de la communauté Kaggle. Les libellés de certaines réponses ont changé entre les éditions : les regroupements retenus sont décrits dans [decisions-modelisation.md](decisions-modelisation.md).
