# Performance et accessibilité - Projet IA (Kaggle Survey)

## Performance

### Volumétrie et taille
| Élément | Valeur |
|---|---|
| Répondants (`Fact_Repondants`) | 70 006 lignes |
| Réponses aux questions d'outils (`Fact_Usage_Outils`) | 533 319 lignes |
| Taille de la copie locale des données (`cache.abf`) | 3,20 Mo |
| Temps d'actualisation complet (lecture des trois fichiers CSV, unpivot, harmonisation, dimensions et faits) | environ 2 min 10 s |

### Temps d'affichage des visuels
Mesures réalisées avec l'Analyseur de performances de Power BI Desktop (version 2.159), après « Actualiser les visuels » sur chaque page, avec les filtres par défaut (édition 2022, thème Langages). Les requêtes d'une même page s'exécutent ensemble, ce qui rapproche leurs durées. Temps total par visuel, en millisecondes.

| Page | Visuels les plus lents (ms) | Autres visuels (ms) |
|---|---|---|
| Vue d'ensemble | Salaire moyen estimé 341 ; Outil n°1 337 ; Pays représentés 334 | Répondants 316 ; Segment d'année 312 ; Répondants par édition 271 ; Top 8 langages 270 ; Niveau d'études 269 ; Carte des pays 201 |
| Outils et tendances | % d'utilisation par outil 213 ; Évolution top 5 213 ; Matrice 186 | |
| Salaires et profils | Segment d'axe 228 ; Répartition par tranche de salaire 176 ; Salaire moyen estimé 176 | |
| Fiche pays (France) | Plus de 5 ans d'expérience 232 ; Salaire moyen estimé 230 ; Part des répondants 228 | |
| Info-bulle (survol) | Répondants 90 ; Évolution 89 ; Utilisation 88 | |

**Lecture :** tous les visuels s'affichent en moins de 350 ms, bien en dessous du repère de 1 seconde. Aucune optimisation supplémentaire n'est nécessaire. Les mesures ont été prises sur le poste de développement : les durées dans le service Power BI peuvent différer.

### Choix qui limitent le coût
* Modèle en étoile, relations un-à-plusieurs unidirectionnelles, aucune relation bidirectionnelle.
* Tables de faits étroites (7 et 8 colonnes entières), clés entières, dimensions du profil réduites à 350, 299 et 72 lignes.
* Clés du profil calculées à partir des ordres de tri : le fait les recalcule sans jointure lors de l'actualisation.
* Date et heure automatiques désactivées, colonnes inutiles supprimées.
* Neuf visuels de données au maximum par page, filtres « N premiers » sur les classements (8 outils, 15 pays, 5 métiers).
* Mode réactif désactivé sur les graphiques.

## Accessibilité
| Critère | Réalisation |
|---|---|
| Texte de remplacement | Renseigné sur les cartes, graphiques, segments et boutons. Les zones de texte et les formes décoratives n'en ont pas |
| Ordre de tabulation | Défini sur chaque page : titre, segments, indicateurs, graphiques, navigation |
| Contraste du texte | Au moins 5,3 pour 1 pour tous les textes de la palette (seuil de 4,5 pour 1) |
| Contraste des éléments graphiques | Au moins 3,7 pour 1 (seuil de 3 pour 1) : barres violettes 3,7, courbes de 3,7 à 10,3 |
| Information portée par la couleur seule | Aucune : les hausses et baisses portent un signe + ou −, et les valeurs sont écrites |
| Titres | Titre de chaque page (20 points) et de chaque graphique (14 points) |
| Taille de police | 10 points au minimum |
| Navigation au clavier | Vérifiée avec la touche Tab sur chaque page : le focus suit l'ordre de tabulation défini |

### Limites
Le rapport n'a pas été testé avec un lecteur d'écran, ni dans le service Power BI. Il n'a pas de disposition pour mobile.
