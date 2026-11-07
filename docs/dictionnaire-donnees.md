# Dictionnaire de données - Projet IA (Kaggle Survey)

Modèle : 2 tables de faits et 7 dimensions, issues du Kaggle Machine Learning & Data Science Survey 2020 à 2022 (usage non commercial). Les colonnes de type « clé » sont masquées dans la vue Rapport. Les mesures sont décrites dans `mesures-dax.md`.

## Tables de faits

### Fact_Repondants (70 006 lignes) : une ligne par répondant
| Colonne | Type | Description |
|---|---|---|
| RepondantID | Entier | Identifiant construit : année × 1 000 000 + numéro de ligne (clé dégénérée) |
| AnneeKey | Entier | Année de l'enquête (vers Dim_Annee) |
| PaysKey | Entier | Clé vers Dim_Pays |
| DemographieKey | Entier | Clé vers Dim_Demographie |
| EmploiKey | Entier | Clé vers Dim_Emploi |
| ExperienceKey | Entier | Clé vers Dim_Experience |
| SalaireKey | Entier | Clé vers Dim_Salaire |

### Fact_Usage_Outils (533 319 lignes) : une ligne par répondant et par option cochée
Mêmes colonnes que Fact_Repondants, plus :
| Colonne | Type | Description |
|---|---|---|
| ReponseKey | Entier | Clé vers Dim_Reponse (thème et option cochée) |

## Dimensions

### Dim_Annee (3 lignes)
| Colonne | Type | Description |
|---|---|---|
| Annee | Entier | 2020, 2021 ou 2022 (clé) |
| Edition | Texte | Libellé d'affichage (« 2020 ») |

### Dim_Pays (68 lignes)
| Colonne | Type | Description |
|---|---|---|
| PaysKey | Entier | Clé |
| Pays | Texte | Nom français d'affichage |
| PaysEN | Texte | Nom anglais, utilisé pour la carte (vide pour « Autre pays » et « Non communiqué ») |
| Region | Texte | 12 régions + « Non précisé » |
| Continent | Texte | 5 continents + « Non précisé » |
| EstPaysReel | Texte | « Oui » ou « Non » (exclut « Autre pays » et « Non communiqué ») |

### Dim_Demographie (350 lignes)
| Colonne | Type | Description |
|---|---|---|
| DemographieKey | Entier | Clé calculée : ordre âge × 10 000 + ordre genre × 100 + ordre études |
| Age, AgeOrdre | Texte, Entier | Tranche d'âge (11) et son ordre de tri |
| Genre, GenreOrdre | Texte, Entier | Genre (5) et son ordre de tri |
| Etudes, EtudesOrdre | Texte, Entier | Niveau d'études (8) et son ordre de tri |

### Dim_Emploi (299 lignes)
| Colonne | Type | Description |
|---|---|---|
| EmploiKey | Entier | Clé calculée : ordre métier × 100 + ordre secteur |
| Metier, MetierOrdre | Texte, Entier | Métier harmonisé (19) et son ordre de tri |
| Secteur, SecteurOrdre | Texte, Entier | Secteur d'activité (20, dont « Non posée » en 2020) et son ordre de tri |

### Dim_Experience (72 lignes)
| Colonne | Type | Description |
|---|---|---|
| ExperienceKey | Entier | Clé calculée : ordre expérience de codage × 100 + ordre expérience ML |
| ExperienceCodage, ExperienceCodageOrdre | Texte, Entier | Années de pratique du code (9) et ordre de tri |
| ExperienceGroupe | Texte | « 5 ans et moins », « Plus de 5 ans » ou « Non répondu » |
| ExperienceML, ExperienceMLOrdre | Texte, Entier | Années de pratique du machine learning (10) et ordre de tri |

### Dim_Salaire (26 lignes)
| Colonne | Type | Description |
|---|---|---|
| SalaireKey | Entier | Clé |
| Tranche, Ordre | Texte, Entier | Tranche harmonisée de l'enquête (25 + « Non communiqué ») et son ordre de tri |
| Groupe, GroupeOrdre | Texte, Entier | 7 groupes pour les graphiques (« < 10 k$ » à « > 250 k$ ») + « Non communiqué » |
| BorneBasseUSD, BorneHauteUSD | Entier | Bornes de la tranche en dollars (haute vide pour la tranche ouverte) |
| MilieuUSD | Entier | Milieu de tranche utilisé pour l'estimation (600 000 pour « 500 000 et plus » ; vide si non communiqué) |
| Communique | Booléen | Vrai si un salaire a été déclaré |

### Dim_Reponse (78 lignes)
| Colonne | Type | Description |
|---|---|---|
| ReponseKey | Entier | Clé |
| Theme | Texte | Langages, Environnements (IDE), Frameworks ML, Cloud ou Outils BI |
| Option | Texte | Outil ou réponse cochée (harmonisée entre les années) |
| EstOutil | Booléen | Faux pour « None » et « Other » |

## Valeurs particulières
* « Non répondu » ou « Non renseigné » : absence de réponse. « Non posée » : question absente en 2020 (secteur).
* Les options non proposées une année n'ont aucune ligne cette année-là : une cellule vide n'est pas un 0 %.