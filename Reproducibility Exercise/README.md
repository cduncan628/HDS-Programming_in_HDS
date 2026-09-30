# Reproducibility Exercise: Diabetes Risk Factor Analysis

## Purpose
Purpose – Demonstrate a clean, end‑to‑end data‑science pipeline that:
1. Loads the provided reference example diabetes dataset (or any dataset with the same schema).
2. Validates the data (column names, row count, missing‑value conventions).
3. Pre‑processes impossible zeros as missing values (NaN).
4. Produces descriptive statistics, visualizations and simple inferential tests that describe how the eight clinical measurements differ between patients with (Outcome = 1) and without (Outcome = 0) diabetes.

The notebook does not train a predictive model, but the data is organized in a way that a model could be added later.

## Description of Analysis
| Step | What the notebook does | Key code / markdown |
|------|------------------------|--------------------|
| **Setup / Environment** | Imports libraries, prints their versions, sets a global random seed (`SEED = 78`). | `import …` + version‑check block |
| **Configuration** | Defines constants (file name, expected column order, expected row count, etc.). | `SEED`, `file_name`, `EXPECTED_COLUMNS`, … |
| **Data source / provenance** | Describes the original source (UCI / Kaggle copy of the Pima Indians Diabetes Database) and the role of the CSV file. | “Data Source / Provenance” markdown |
| **Data loading** | Looks for `Example Dataset_Diabetes.csv` in `./data/` (preferred) or the notebook’s folder; raises a clear error if not found. | `if os.path.exists(...):` |
| **Data validation** | Checks column names & order, row count, numeric types, no NaNs, no duplicates, correct `Outcome` values, outcome counts, no negative values, sensible ranges for age, glucose & BMI. Stops execution on first failure. | `check()` helper |
| **Zero‑value audit** | Counts zeros in columns where a zero is impossible (Glucose, D_BP, Skin_Thickness, Insulin, BMI, Age). | `zero_table` |
| **Data preparation** | Copies the raw frame (`df_raw`) → `df_clean` and replaces impossible zeros with `NaN`. | `df_clean[ZERO_IMPOSSIBLE] = …` |
| **Descriptive statistics** | - Overall `df_clean.describe()`   <br> - Group‑wise mean ± SD for each predictor (by `Outcome`). | `group_stats = df_clean.groupby("Outcome")…` |
| **Vizualisations** | - Histogram of Glucose <br> - Histogram of BMI <br> - (Boxplot of Glucose by Outcome, Scatter of Glucose vs. BMI – in the full notebook) | `plt.hist(...)` |
| **(Further steps – not shown in excerpt)** | Correlation matrix, two‑sample hypothesis tests, bootstrap confidence intervals, etc. | Subsequent markdown sections |

## Dataset source/provenance
| Item | Detail |
|------|--------|
| **File name** | `Example Dataset_Diabetes.csv` (also `data/Example Dataset_Diabetes.csv`) |
| **Original source** | *Pima Indians Diabetes Database* – National Institute of Diabetes and Digestive and Kidney Diseases (NIDDK). Public mirrors on the UCI Machine Learning Repository and Kaggle. |
| **Subjects** | 768 women of Pima Indian heritage, ages 21 – 81, living near Phoenix, AZ. |
| **Columns** | `Pregnancies, Glucose, D_BP, Skin_Thickness, Insulin, BMI, Pedigree, Age, Outcome` (see table below) |
| **Outcome** | `Outcome = 1` → diabetes, `Outcome = 0` → no diabetes |
| **Role in this repo** | Example data used to validate the workflow. Any CSV with the same nine columns can be swapped in and only the configuration constants need updating. |

Known issue: a value of 0 in Glucose, D_BP, Skin_Thickness, Insulin, BMI (and Age) is not physiologically possible and is treated as a missing value.

## Required software, libraries, and package versions
| Library | Minimum version (tested) |
|---------|---------------------------|
| Python | **3.13.15** |
| pandas | **2.2.3** |
| numpy | **2.1.3** |
| scipy | **1.16.3** |
| matplotlib | **3.10.0** |
| seaborn | **0.13.2** |
| statsmodels | **0.15.0** |

Optional: Jupyter or Google Colab for interactive visualization.

## Setup/installation instructions
1. Clone / copy the repository

```python
git clone https://github.com/your-org/diabetes-risk-factor-analysis.git
cd diabetes-risk-factor-analysis
```

2. Install required packages

```python
pip install -r requirements.txt
```

3. Place the dataset
Preferred: create a folder called data in the project root and copy the CSV there:
```python
diabetes-risk-factor-analysis/
├─ data/
│   └─ Example Dataset_Diabetes.csv
└─ starter_diabetes_risk_factor_analysis (3).ipynb
```

4. Launch Jupyter / Colab

```python
jupyter notebook "diabetes_risk_factor_analysis.ipynb"
```

In Google Colab you can simply upload the notebook and the CSV.

## Instructions for executing the notebook from start to finish
| Action | How |
|--------|-----|
| **Restart kernel** | In Jupyter: `Kernel → Restart & Run All` (or in Colab: *Runtime → Restart runtime* then *Runtime → Run all*) |
| **Run cells sequentially** | The notebook is written to stop on the first validation failure, so running top‑to‑bottom guarantees that each step has the required inputs. |
| **Key checkpoints** | <ul><li>**Setup** – version print‑out (should show “OK” for each library).</li><li>**Validation** – a series of `PASS:` messages; any `FAIL:` aborts execution.</li><li>**Zero audit** – a `zero_table` DataFrame summarizing impossible zeros.</li><li>**Descriptive tables** – `df_clean.describe()` and group‑wise mean ± SD.</li><li>**Plots** – two histogram figures appear inline.</li></ul> |
| **Customizing** | If you replace the dataset, edit the constants in the **Configuration** section (`EXPECTED_ROW_COUNT`, `EXPECTED_OUTCOME_COUNTS`, possibly `ZERO_IMPOSSIBLE`). |


## Expected Outputs  

| # | Step (what the cell does) | Expected output (what you’ll see) |
|---|---------------------------|-----------------------------------|
| 1 | **Package versions** | Python 3.13.15, OS Linux, and a line‑by‑line “OK” for each required library. |
| 2 | **Seed confirmation** | `Setup complete. Seed = 78` |
| 3 | **Load CSV** | `Loaded file from: Example Dataset_Diabetes.csv` + the first 5 rows of `df_raw`. |
| 4 | **Data‑validation** | A series of `PASS:` messages for column names, row count (768), numeric types, no NaNs, correct outcome coding, etc., followed by the DataFrame shape and dtype list. |
| 5 | **Zero‑value audit** | Small table (`zero_table`) showing how many zeros each column has and whether zeros are impossible. |
| 6 | **Missing‑value report** | Counts of `NaN`s after converting impossible zeros (e.g., Glucose 5, Insulin 374, …). |
| 7 | **Sample of clean data** | 10 random rows from `df_clean` (shows `NaN`s where zeros were replaced). |
| 8 | **Overall descriptive stats** | `df_clean.describe()` table (count, mean, std, min, 25 %, 50 %, 75 %, max). |
| 9 | **Group‑wise mean ± SD** | Table of means and SDs for each predictor split by `Outcome = 0` vs. `1`. |
|10| **Mean glucose by group** | Text: <br>`No diabetes: n = 500  mean = 110.6  SD = 24.8` <br>`Diabetes:    n = 268  mean = 142.3  SD = 29.6` |
|11| **Skewness & Shapiro‑Wilk** | Skewness values (≈ ‑0.08 / 0.31) and two p‑values (both < 0.001). |
|12| **Q‑Q plots** | Two side‑by‑side Q‑Q figures (one for each outcome). |
|13| **Levene’s test** | `Levene's test statistic: …  p‑value: …` and a message indicating “Welch’s t‑test” (variances unequal). |
|14| **Two‑sample t‑test** | Difference = 31.7 mg/dL, 95 % CI 27.5 – 35.9, t‑stat ≈ 20.3, p ≈ 2.8e‑41, test name (“Welch’s t‑test”). |
|15| **Effect size** | `Cohen's d: 1.19` |
|16| **Logistic‑regression data** | Counts of total subjects, complete cases (724), excluded rows, and outcome frequencies. |
|17| **Binary‑outcome check** | `PASS - Assumption 1: Outcome is binary (0/1)` |
|18| **Events‑per‑variable** | `Events per variable (EPV): 45.3` and a PASS message. |
|19| **VIF table** | VIF values for each predictor (all ≈ 1.0‑1.3) and a PASS line. |
|20| **Box‑Tidwell test** | p‑values for each `X·ln(X)` term; warns that `Pedigree` and `Age` show non‑linearity. |
|21| **Logistic‑regression summary** | Full StatsModels table (coefficients, SE, z, p, 95 % CI, pseudo‑R² = 0.278, LLR p ≈ 4.7e‑53). |
|22| **Overall model test** | “Likelihood‑ratio test: G = 259.08 df = 6 p = 4.7e‑53” and “McFadden pseudo R‑squared: 0.278”. |
|23| **Cook’s distance** | Cut‑off = 0.0055, 42 subjects above cut‑off, max = 0.057. |
|24| **Histograms** | Two figures: (a) Glucose distribution, (b) BMI distribution. |
|25| **Box‑plot (Glucose vs Outcome)** | Figure titled *2‑Hour Plasma Glucose by Diabetes Outcome* with means shown. |
|26| **Scatter (Glucose vs BMI)** | Figure titled *Comparison of Glucose to BMI* coloured by outcome. |
|27| **Correlation heat‑map** | Heat‑map of Spearman correlations with values annotated. |
|28| **Hosmer‑Lemeshow table** | Decile table of subjects, observed vs. expected cases. |
|29| **Hosmer‑Lemeshow test** | “Hosmer‑Lemeshow statistic: 12.0 df = 8 p = 0.1511” → PASS. |
|30| **Plain‑language sentences** | Six bullet sentences summarizing the adjusted odds ratios (e.g., “Pregnancies: each extra pregnancy → 13 % higher odds of diabetes (OR 1.13, p = 0.0004, statistically significant)”). |
|31| **Final results table** | Two‑column DataFrame listing the key numbers you provided (subjects, prevalence, means, mean difference, CI, test name, p‑value, Cohen’s d, complete‑case count, adjusted ORs, Hosmer‑Lemeshow p‑value). |

If any of the validation steps print a `FAIL:` line, the notebook stops and you’ll need to correct the data or configuration. Otherwise the tables and figures above should appear exactly as shown.

## Dataset requirement
| Requirement | Description |
|-------------|-------------|
| **File format** | CSV with *exact column names* (case‑sensitive) as listed in `EXPECTED_COLUMNS`. |
| **Row count** | Default `EXPECTED_ROW_COUNT = 768`. Change this constant if you have a different number of rows. |
| **Outcome distribution** | Default `EXPECTED_OUTCOME_COUNTS = {0: 500, 1: 268}` – edit if your data have a different class balance. |
| **Numeric columns** | All columns must be numeric (int or float). |
| **Zero convention** | If any of the columns `Glucose, D_BP, Skin_Thickness, Insulin, BMI, Age` contain a zero, the notebook treats it as missing. Adjust `ZERO_IMPOSSIBLE` if your data use a different sentinel (e.g., `-1`). |
| **Value ranges** | Minimal sanity checks are performed (`Age` 18‑100, `Glucose` < 300, `BMI` < 80). Modify the `check()` calls if your population falls outside these bounds. |

## Assumptions and limitations
| Assumption | Why it matters |
|------------|----------------|
| **Zeros = missing** | Zero values for Glucose, BP, etc. are physiologically impossible; they are replaced with `NaN`. This improves means/SDs but reduces the effective sample size for those variables. |
| **No imputation** | Missing values are left as `NaN`. The analysis is purely descriptive; any modelling would need an imputation strategy. |
| **Binary outcome only** | The notebook is built around a `0/1` `Outcome`. Multi‑class or continuous outcomes would require restructuring. |
| **Static column order** | Validation checks that the column order matches the expected schema; changing order without updating `EXPECTED_COLUMNS` will raise an error. |
| **Statistical tests not shown** | The excerpt ends before inferential tests; those sections (t‑tests, logistic regression, bootstrap) are assumed to be present in the full notebook. |
| **Small, homogeneous sample** | The data represent a specific demographic (Pima women); conclusions may not generalize to other populations. |
| **No external dependencies** | All code runs locally; reproducibility depends solely on the versions listed. |

## Computational environment information
| Item | Value |
|------|-------|
| **OS** | Linux 6.6 (output of `platform.system()` / `platform.release()`) |
| **Python interpreter** | `3.13.15` |
| **CPU** | Any modern x86‑64 processor – the notebook is not computationally intensive. |
| **RAM** | ≤ 2 GB is sufficient (the dataset is ~8 KB). |
| **GPU** | Not required. |
| **Colab** | Tested in Google Colab. |
| **Random seed** | `SEED = 78` (ensures deterministic sampling, bootstrap, etc.). |