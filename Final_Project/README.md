# Predicting Phase 3 Clinical Trial Success — a Synthetic-Data Decision-Support Notebook

> ⚠ **DISCLAIMER:** This project uses **100% synthetic data**. It cannot be used to predict real-world trial outcomes or to support clinical, regulatory, investment, or portfolio decisions. It is an educational demonstration. 

The code in this repository was extensively adapted and modified from the original notebooks authored by Dartmouth faculty member **Dr. Jamie Fairclough**.

## Overview
This notebook shows an end-to-end workflow for a sponsor deciding whether to proceed with, redesign, or cancel a Phase 3 trial. It asks two questions:
1. Which trial characteristics are associated with Phase 3 success?
2. How accurately can they predict whether a specific trial will succeed?

The workflow covers upload and validation, cleaning, descriptive statistics, exploratory plots, inferential tests, four supervised model families, precision-focused model selection, threshold setting, and a what-if scenario tool.

## The data
- **File:** `data/synthetic_clinical_trial_phase3.csv`: 42,000 trials, 17 columns (1 ID, 15 predictors, 1 target `trial_success`).
- **Generator:** `src/synthetic_dataset_generator.py` (seed 973).
- **Why synthetic:** real Phase 3 outcome data are scattered and proprietary; synthetic data are reproducible and shareable; the planted truth lets us check whether methods recover it; and the generator avoids the clipped "spike" values in the reference file.
- **Lineage:** the structure and effect sizes follow a [reference dataset](https://www.kaggle.com/datasets/sergionefedov/what-predicts-a-phase-3-trials-success) that is itself documented as 100% synthetic and calibrated to the probability-of-success literature (e.g., Wong, Siah & Lo, 2019). No real trial is reproduced.
- **Built in:** ~55% success overall; therapeutic area, biomarker use, Phase 2 signal, endpoint hardness, and sponsor type drive success; the accuracy ceiling is AUC ≈ 0.68.

### Data dictionary
| Column | Type | Description |
|---|---|---|
| `trial_id` | ID | `SYN000000…`; not a predictor |
| `therapeutic_area` | categorical (9) | oncology, cardiovascular, cns, infectious_disease, immunology, metabolic, respiratory, rare_disease, vaccine |
| `endpoint_type` | categorical (3) | hard_survival, clinical, surrogate |
| `sponsor_type` | categorical (4) | large_pharma, mid_pharma, small_biotech, academic |
| `phase2_effect_size`, `phase2_pvalue_strength` | numeric 0–10 | Strength of Phase 2 signal / evidence |
| `enrollment` | count | Patients enrolled (right-skewed) |
| `statistical_power` | numeric | Planned power (0.40–0.995) |
| `prior_approvals_target`, `years_in_development` | count / numeric | Program context |
| `uses_biomarker`, `novel_mechanism`, `randomized`, `double_blind`, `active_comparator`, `orphan_designation` | binary | Design and regulatory flags |
| `trial_success` | binary target | 1 = met primary endpoint |

## Repository structure
```
├── README.md
├── notebooks/
│   └── final_project_v2.ipynb
│   └── google_colab_links.md
├── data/
│   ├── synthetic_clinical_trial_phase3.csv
│   ├── data_dictionary_clinical_trial.csv
│   └── reference/
│       └── clinical_trial_phase3_success.csv
└── src/
    └── synthetic_dataset_generator.ipynb
```

## Getting started
**Google Colab:** open the notebook ([Open in Colab](https://colab.research.google.com/drive/1PNzMgEonsWBhnq1cnxW0E1VdPfxKxA3o?usp=sharing)), choose *Runtime → Run all*, and select `synthetic_clinical_trial_phase3.csv` when the upload prompt appears.

**Local:**
```bash
git clone <your-repo-url> && cd <repo>
python -m venv .venv && source .venv/bin/activate      # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook notebooks/final_project_v2.ipynb
```
Outside Colab the notebook reads `DATA_PATH` (default `data/synthetic_clinical_trial_phase3.csv`).

**Regenerate the data (optional):**
**Google Colab:** open the synthetic dataset generator ([Open in Colab](https://colab.research.google.com/drive/1jctyVbUDuzcQX5szIJmeyBQZquC5G72D?usp=sharing)), choose *Runtime → Run all*, and download the created `synthetic_clinical_trial_phase3.csv` file for upload to the project notebook.

The same seed should reproduce the same file. A different seed gives different numbers.

**Expected run time:** 16-17 minutes (SVM and XGBoost searches are the slowest).

## Reproducibility
- **Python 3.13** (tested on Colab and local environments).  
- **Pinned package versions** (shown when you run the notebook):
pandas 2.2.3
numpy 2.1.3
scikit-learn 1.6.1
scipy 1.16.3
statsmodels 0.15.0
matplotlib 3.10.0
seaborn 0.13.2
xgboost 3.4.1
shap 0.52.0
imbalanced-learn 0.14.2

## Notebook workflow
| Section | What it does |
|---|---|
| About the data / Considerations | Synthetic-data disclaimer and how to read the numbers |
| Setup and settings | Imports, user-defined settings with validation |
| Load, validate, clean | Upload, schema and range checks, cleaning log |
| Descriptives and EDA | Summary statistics, distributions, success rates, correlations, 3D surfaces |
| Inferential statistics | Phi-K, linearity checks, Levene, Mann-Whitney U, chi-square / Fisher |
| Models | Logistic regression, SVM (linear and RBF), decision tree, XGBoost (each baseline and tuned) |
| Evaluation and selection | CV comparison, precision@30% selection with tie rule, threshold, permutation importance, SHAP |
| Decision support | Scenario tool, findings, recommendations |

## Key methods
- **Inferential statistics:** Mann-Whitney U with rank-biserial r; chi-square with Cramér's V (Fisher's exact when the 2×2 expected count < 5).
- **LaTeX formulas** are shown for both tests, the evaluation metrics, AUC (via the Mann-Whitney identity), and each model.
- **Conditional logic:** class weighting, skew transforms, imputation, and exact vs. scalable SVM adapt to the training data.
- **Primary metric:** precision among the top 30% of trials (5-fold CV), chosen because funding a failed Phase 3 trial is the costliest error. Average precision, AUC-ROC, F1, recall, and accuracy are secondary.

## Results (synthetic data)
| Item                                            | Value |
|-------------------------------------------------|-------|
| Realized success rate                           | **55.0%** (55.04 % of the 42 000 trials succeeded) |
| Selected model                                  | **Logistic Regression (tuned)** |
| CV / test precision@30 %                        | **0.725** / **0.716** |
| Test AUC‑ROC.                                   | **0.667** |
| Chosen threshold → precision, share funded      | **0.603 → 68.9% precision, 39.4% of trials funded** |

## Using your own dataset
Provide a CSV with the 17 columns above, matching names, levels, and units. Validation flags mismatches. Re-set `TARGET_PRECISION`, `FUNDING_RATE`, and `MIN_RECALL`, then rerun everything. **The narrative interpretations quote numbers from the synthetic dataset. With your own data, results may vary and the text must be rewritten.**

## Limitations
- Synthetic data: findings reflect the generator, not real trials.
- Associations are not causes.
- Predictors are mostly independent by construction. For example, first-in-class trials can have prior approvals, and orphan designation is unrelated to rare disease.
- The generator is additive and logistic, which favours logistic regression.
- No interim, safety, mechanism, competitive, or cost data.

## References
- Sergey Nefedov. (Sep 2026). *What Predicts a Phase 3 Trial's Success?*. Kaggle. https://www.kaggle.com/datasets/sergionefedov/what-predicts-a-phase-3-trials-success
- Wong, C. H., Siah, K. W., & Lo, A. W. (2019). Estimation of clinical trial success rates and related parameters. *Biostatistics, 20*(2), 273–286.
- Chen, T., & Guestrin, C. (2016). XGBoost: A scalable tree boosting system. *KDD*.
- Lundberg, S. M., & Lee, S.-I. (2017). A unified approach to interpreting model predictions. *NeurIPS*.
- Baak, M., et al. (2020). A new correlation coefficient between categorical, ordinal and interval variables with Pearson characteristics (Phi-K). *Computational Statistics & Data Analysis*.

## AI-usage statement
**Claude (Anthropic)** was used to draft this README.

All AI-generated text and code was reviewed, run, and edited. AI was not used to generate results. Every number reported in the notebook comes from executing the code on the synthetic dataset. The AI tools did not have access to real patient or trial data, and none was used.

