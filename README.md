
---

## Charte Graphic et Design Figma (Regle 0.6)

Ce projet integre une approche Figma-to-PowerBI pour garantir une interface epuree et performante :

### Configuration du Canvas et Grille
* Format du cadre : 1280 x 720 px (Ratio 16:9 adapte aux ecrans standards).
* Grille de mise en page : 12 colonnes | Marges : 24 px | Gouttieres : 16 px.
* Composants cles (Auto layout) : 
  * Bandeau d'en-tete (Hauteur : 64 px).
  * Barre de filtres laterale ou superieure (Hauteur : 56 px).
  * Cartes KPI : Largeur fixe de 296 px (Calcul exact : 1280 - 48 - 3x16 = 1184 px, divise par 4 cartes cote a cote) | Hauteur : 96 px | Rayon de courbure : 8 | Ombre portee legere.

### Hierarchie Typographique (Police : Segoe UI)
* Titre de page : Taille 24 | Graisse : Semibold
* Titre de visuel : Taille 14 | Graisse : Semibold
* Valeur KPI : Taille 32 | Graisse : Bold
* Libelle / Axe : Taille 11 | Graisse : Regular
* Note / Source : Taille 10 | Graisse : Regular

### Organisation des Pages du fichier Figma
1. Cover : Page de garde du projet.
2. Foundations : Couleurs thematiques (Sombre, Violet, Cyan), typographies et grilles.
3. Components : Boutons, cartes KPI vides, et en-tete reutilisables.
4. Wireframes : Maquettes basse fidelite en nuances de gris pour valider la hierarchie visuelle.
5. Final : Maquettes haute fidelite pretes pour l'export PNG vers Power BI.
