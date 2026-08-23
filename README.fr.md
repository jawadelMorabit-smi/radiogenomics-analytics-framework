[![Readme in English](https://img.shields.io/badge/docs-English-blue)](README.md)
[![Kaggle](https://img.shields.io/badge/dataset-RSNA--MICCAI-20BEFF?logo=kaggle&logoColor=white)]
![Python](https://img.shields.io/badge/Python-3.10%2B-blue?logo=python&logoColor=white)

# Radiogenomics Analytics Framework

**IRM → Radiomique / CNN 3D → Prédiction génomique (méthylation MGMT)**

Deux approches complémentaires pour prédire le **statut de méthylation du
promoteur MGMT** dans le glioblastome à partir d'IRM multi-modales — sans
biopsie invasive. Construit sur le jeu de données
[RSNA-MICCAI Brain Tumor Radiogenomic Classification](https://www.kaggle.com/competitions/rsna-miccai-brain-tumor-radiogenomic-classification).

> **Qu'est-ce que la radiogénomique ?** Un domaine interdisciplinaire qui étudie
> les associations statistiques entre les caractéristiques d'imagerie
> quantitatives (**radiomique**) et les caractéristiques génomiques/moléculaires
> des tumeurs. La méthylation du promoteur MGMT est un biomarqueur pronostique
> majeur : les tumeurs méthylées répondent mieux à la chimiothérapie par
> témozolomide.

## Résultats clés (CNN 3D)

> Prédire MGMT à partir de l'IRM seule est réputé très difficile — dans la
> littérature, la plupart des modèles restent proches du hasard (AUC 0,50–0,65).

| Métrique | Valeur |
|---|---|
| **AUC test — ensemble de 5 modèles** | **≈ 0,64** |
| AUC en validation croisée (5 blocs, niveau fiable) | 0,61 ± 0,04 |
| Balanced accuracy (ensemble) | ≈ 0,60 |

Démarche honnête tout au long : seuil réglé sur la validation uniquement, test
évalué une seule fois, validation croisée comme estimation fiable. Un résultat
négatif documenté (le multi-modalités *dégrade* les performances, modalités non
recalées) et les ablations (pondération de classes / seuil / augmentation) sont
détaillés dans le notebook.

---

## Sommaire

- [Pipeline](#pipeline)
- [Méthodologie](#méthodologie)
- [Structure du projet](#structure-du-projet)
- [Installation](#installation)
- [Exécution du notebook](#exécution-du-notebook)
- [Sorties](#sorties)
- [Avertissement médical](#avertissement-médical)

---

## Pipeline

Le notebook (`radiogenomics-research.ipynb`) s'organise en 10 sections
séquentielles :

| Section | Étape | Description |
|---|---|---|
| A | Installation & configuration | Environnement (toutes les bibliothèques sont disponibles sur Kaggle). |
| B | Compréhension des données | Labels cliniques, équilibre des classes, vue des modalités IRM. |
| C | Prétraitement des images | Pipeline robuste pour l'IRM multi-modale (FLAIR / T1 / T1ce / T2). |
| D | Approximation de la région tumorale | Construction d'un pseudo-ROI (masques de segmentation réels indisponibles). |
| E | Extraction des caractéristiques radiomiques | Trois catégories de descripteurs quantitatifs. |
| F | Sélection de caractéristiques | Réduction d'espace haute dimension + LASSO, ensemble consensus. |
| G | **Analyse radiogénomique** | Tests d'association statistique, analyse de fusion multi-modalités, plausibilité biologique. |
| H | Modélisation prédictive | Régression logistique vs Random Forest, avant/après sélection, CV stratifiée 5 blocs (sans fuite). |
| I | Évaluation | Courbes ROC, matrices de confusion, métriques détaillées du meilleur modèle. |
| J | Interprétation | Importance des variables & tableau de bord de pertinence biologique. |

## Méthodologie

- **Aucune fuite de données** : toutes les comparaisons utilisent une validation
  croisée stratifiée à 5 blocs.
- **Gestion honnête du ROI** : la radiomique réelle exige une segmentation ; les
  masques étant indisponibles, une approximation documentée (pseudo-ROI) est
  utilisée.
- **Analyse de fusion** : quantifie si combiner les séquences IRM améliore la
  prédiction par rapport aux séquences seules.
- **Modèles interprétables d'abord** : régression logistique en baseline,
  Random Forest pour les interactions non linéaires — avec importance des
  variables pour revue biologique.

## Structure du projet

```text
.
├── jaouad-el-morabit.ipynb        # Approche CNN 3D — sujet du prof (EXÉCUTÉ, résultats inclus)
├── radiogenomics-research.ipynb   # Framework radiomique + ML classique (sections A–J)
├── Jaouad_El_Morabit.pdf          # Rapport de projet (français)
├── requirements.txt               # Dépendances Python
└── .gitignore
```

## Les deux notebooks

### 1. `jaouad-el-morabit.ipynb` — CNN 3D (exécuté ✅)

Pipeline complet de deep learning en 18 sections : chargement DICOM → volumes
3D → CNN 3D → évaluation, avec 5 optimisations testées une par une
(pondération des classes, seuil de décision, meilleures coupes + z-score,
augmentation de données, validation croisée 5 blocs) puis deux leviers finaux
(fusion multi-modalités, ensembling). Toutes les cellules exécutées sur Kaggle,
sorties et figures intégrées.

### 2. `radiogenomics-research.ipynb` — Framework radiomique

L'approche alternative interprétable (sections A–J décrites ci-dessous). C'est
la source du notebook ; exécutez-le une fois sur le dataset pour régénérer les
sorties.

### Détail du pipeline radiomique

## Installation

Prérequis : **Python ≥ 3.10** avec pip.

```bash
git clone https://github.com/jawadelMorabit-smi/radiogenomics-analytics-framework.git
cd radiogenomics-analytics-framework
pip install -r requirements.txt
jupyter notebook radiogenomics-research.ipynb
```

Dépendances : NumPy, pandas, SciPy, scikit-learn, statsmodels, OpenCV,
scikit-image, PyDICOM, Matplotlib, seaborn.

## Exécution du notebook

Le pipeline a été développé pour **l'environnement Kaggle**, où le jeu RSNA-MICCAI
est monté sous `/kaggle/input`. Deux options :

1. **Recommandé — Kaggle :** ouvrir le notebook sur
   [kaggle.com/code](https://www.kaggle.com/code), attacher le dataset de la
   compétition et *Run All*. Le quota CPU/GPU gratuit suffit.
2. **En local :** télécharger les données RSNA-MICCAI depuis Kaggle et mettre à
   jour `BASE_PATH` en section B vers votre copie locale.

> Note : ce dépôt contient le notebook **sans sorties pré-calculées** — exécutez-le
> une fois sur le dataset pour régénérer tous les tableaux et figures.

## Sorties

La section finale sauvegarde 10 figures couvrant toute l'analyse : distribution
des classes, modalités IRM, prétraitement, approximation du ROI, sélection de
caractéristiques, association radiogénomique (volcano plot), fusion des
modalités, comparaison des modèles, évaluation (ROC / matrice de confusion) et
tableau de bord d'interprétation.

Le notebook CNN exécuté contient en plus 7 figures intégrées (courbes
d'entraînement, ROC, matrices de confusion) et son exécution complète est
consultable sur [Kaggle](https://www.kaggle.com/code/jaouadelmorabit/jaouad-el-morabit).

## Avertissement médical

Ce projet est un cadre de **recherche/pédagogie**. Ce n'est pas un dispositif
médical et il ne doit pas servir à des décisions cliniques.

---

*Auteur : Jaouad El Morabit — Master BIAM 2025-2026, Imagerie biomédicale.*
