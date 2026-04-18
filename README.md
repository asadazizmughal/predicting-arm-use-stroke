# Predicting Real-World Arm Use After Stroke

**Course:** Python-R-Git
**Program:** Master STAPS — Sciences Technologies Mouvement
**Institution:** EuroMov Digital Health in Motion, University of Montpellier
**Author:** Asad NADEEM
**Supervisors:** Karima BAKHTI (PhD), Makii MUTHALIB (PhD), Nicolas SUTTON-CHARANI (PhD)
**Academic year:** 2025–2026

---

## 1. Scientific Question

After a stroke, approximately 77% of patients present motor deficits of the upper limb. Even when sensorimotor recovery occurs, some patients do not spontaneously use their paretic arm in daily life — a phenomenon known as **learned non-use** (Taub et al., 2006).

This project investigates two questions:

1. **Can clinical motor and functional tests predict how much stroke patients use their paretic arm in daily life at home?**
2. **Can we identify individual patients who underuse their paretic arm relative to their motor capacity?**

## 2. Project Report

The main deliverable is **`nadeem.asad.html`** — a self-contained HTML report (≤ 3000 words, numbered sections, figures, tables, and a clickable TOC).

> **To re-generate the report:** open `nadeem.asad.Rmd` in RStudio and click *Knit*.

## 3. Repository Structure

```
predicting-arm-use-stroke/
│
├── readme.md                  ← this file
├── LICENSE                    ← MIT License
├── nadeem.asad.html           ← final report (deliverable)
├── nadeem.asad.Rmd            ← source of the report
│
├── main.ipynb                 ← Python entry point: full ML pipeline
├── main.Rmd                   ← R entry point: statistical validation
├── main.Rproj                 ← RStudio project file
│
├── data/                      ← raw and merged data files
│   ├── Outcomes_Rearm_01_04_23.xlsx
│   ├── all_day_FuncUse_uni_bi.csv
│   └── merged_dataset.csv     ← produced by main.ipynb
│
├── results/                   ← generated plots and tables (by main.ipynb)
│   ├── model_metrics.csv
│   ├── feature_importance.csv
│   ├── discrepancy_table.csv
│   ├── predicted_vs_actual.png
│   ├── feature_importance.png
│   └── discrepancy_analysis.png
│
├── notebooks/                 ← reserved for exploratory notebooks (optional)
└── sources/                   ← reserved for reusable scripts (optional)
```

## 4. How to Run

### Prerequisites

- **Python 3.9+** with Jupyter
- **R 4.0+** with RStudio
- **Git** (for cloning)

### Installation

```bash
# Clone the repository
git clone https://github.com/asadazizmughal/predicting-arm-use-stroke.git
cd predicting-arm-use-stroke
```

**Python dependencies** (all listed in the first cell of `main.ipynb`):

```bash
pip install pandas numpy matplotlib seaborn scikit-learn openpyxl
```

**R dependencies** (all listed in the first chunk of `main.Rmd`):

```r
install.packages(c("readr", "ggplot2", "dplyr", "corrplot"))
```

### Execution order

The analysis runs in two steps:

1. **Python first** — `main.ipynb` loads the raw data, builds the target variable, merges everything into `data/merged_dataset.csv`, runs the ML pipeline, and saves results to `results/`.
2. **R second** — `main.Rmd` loads `data/merged_dataset.csv` and performs statistical validation.
3. **Report last** — knit `nadeem.asad.Rmd` to produce the final HTML report.

```bash
# Step 1 — Python
jupyter notebook main.ipynb

# Step 2 — R (open in RStudio)
#   Open main.Rproj, then open main.Rmd and click Knit

# Step 3 — Report
#   Open nadeem.asad.Rmd and click Knit
```

## 5. Methodology Summary

### Data

- **30 chronic post-stroke patients** from the ReArm randomized controlled trial (Muller et al., 2021; ClinicalTrials.gov NCT04291573)
- **Clinical variables:** Fugl-Meyer (FMUE), Wolf Motor Function Test (WMFT), Box & Block Test (BBT), Barthel Index, Proximal Arm Non-Use (PANU), demographics
- **Target:** `funcUseRatio` from 7-day wrist accelerometry — ratio of functional movements between paretic and non-paretic arms

### Analysis pipeline

| Step | Language | Purpose |
|------|----------|---------|
| Data loading & merge | Python | `openpyxl` for Excel, `pandas` for CSV |
| Target computation | Python | `numpy` averages across 7 days |
| Statistical validation | R | Shapiro-Wilk, Spearman, Mann-Whitney |
| ML modelling | Python | LOOCV with in-fold imputation, 3 models compared |
| Feature importance | Python | Mean absolute Lasso coefficients across folds |
| Discrepancy analysis | Python | Residual-based learned non-use detection |
| Final report | R Markdown | Self-contained HTML |

### Key methodological choices

- **LOOCV** because the sample size (n = 30) is too small for train/test split
- **In-fold imputation** (training set only) to prevent data leakage — following Prof. Gérard Dray's (IMT Mines Alès) explicit recommendation
- **Regularized regression (Ridge, Lasso)** to handle multicollinearity between motor tests (FMUE-WMFT ρ = 0.91)
- **Non-parametric statistics (Spearman, Mann-Whitney)** because `funcUseRatio` is right-skewed (Shapiro-Wilk p < 0.05)

## 6. Key Findings

| Finding | Statistic |
|---------|-----------|
| **WMFT** predicts real-world arm use | ρ = 0.60, p < 0.001 |
| **BBT** predicts real-world arm use | ρ = 0.58, p < 0.001 |
| **FMUE** predicts real-world arm use | ρ = 0.48, p < 0.01 |
| **PANU** does NOT predict real-world arm use | ρ = -0.13, p = 0.50 |
| No effect of Sex or Paresis side | p > 0.05 |
| Strong multicollinearity between motor tests | ρ > 0.75 |
| ~50% of patients show residual under-use | candidates for learned non-use |

These results confirm the main findings of Le Perf (2022) on an extended sample, and add an individual-level discrepancy analysis not present in the original thesis.

## 7. Code Quality

This project follows the course guidelines:

- **Clear structure:** each notebook is decomposed into logical thematic sections
- **Reproducible:** relative paths throughout, no hard-coded absolute paths
- **Cross-platform:** tested on Windows and macOS
- **Well-commented:** every non-trivial decision is documented inline
- **LLM-assisted but defensible:** all code can be explained and modified on demand
- **Single source of truth:** Python produces `merged_dataset.csv`, R consumes it

## 8. Credits

- **Data:** ReArm protocol (Muller et al., 2021) — CHU Montpellier, CHU Grau du Roi
- **Target variable algorithm:** Leuenberger et al. (2017)
- **Methodological guidance on in-fold imputation:** Prof. Gérard Dray (IMT Mines Alès)
- **Previous work this extends:** Le Perf (2022) Master's thesis, University of Montpellier

## 9. Licence

This project is released under the **MIT License**. See the `LICENSE` file for details.

## 10. Contact

Asad NADEEM — Master STAPS, University of Montpellier
GitHub: [@asadazizmughal](https://github.com/asadazizmughal)
