[![Readme in English](https://img.shields.io/badge/docs-English-blue)](README.md)
[![Kaggle](https://img.shields.io/badge/dataset-RSNA--MICCAI-20BEFF?logo=kaggle&logoColor=white)]
![Python](https://img.shields.io/badge/Python-3.10%2B-blue?logo=python&logoColor=white)

# Radiogenomics Analytics Framework

**IRM → Radiomique → Prédiction génomique (méthylation MGMT)**

Un pipeline **radiogénomique** de bout en bout qui prédit le **statut de
méthylation du promoteur MGMT** dans le glioblastome à partir d'IRM
multi-modales — sans biopsie invasive. Construit sur le jeu de données
[RSNA-MICCAI Brain Tumor Radiogenomic Classification](https://www.kaggle.com/competitions/rsna-miccai-digital-pathology).

> **Qu'est-ce que la radiogénomique ?** Un domaine interdisciplinaire qui étudie
> les associations statistiques entre les caractéristiques d'imagerie
> quantitatives (**radiomique**) et les caractéristiques génomiques/moléculaires
> des tumeurs. La méthylation du promoteur MGMT est un biomarqueur pronostique
> majeur : les tumeurs méthylées répondent mieux à la chimiothérapie par
> témozolomide.

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
├── radiogenomics-research.ipynb   # Notebook pipeline complet (sections A–J)
├── Jaouad_El_Morabit.pdf          # Rapport de projet (français)
├── requirements.txt               # Dépendances Python
└── .gitignore
```

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

## Avertissement médical

Ce projet est un cadre de **recherche/pédagogie**. Ce n'est pas un dispositif
médical et il ne doit pas servir à des décisions cliniques.

---

*Auteur : Jaouad El Morabit — Master BIAM 2025-2026, Imagerie biomédicale.*
