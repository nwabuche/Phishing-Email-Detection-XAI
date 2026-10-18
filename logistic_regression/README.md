# Logistic Regression — Explainable AI for Phishing Email Detection

## 1. Project Overview

This folder contains my Logistic Regression experiments for phishing email detection, developed as part of my master's thesis at the Polytechnic Institute of Cávado and Ave (IPCA), Portugal.

**Thesis Title:** Explainability-Guided Feature Analysis for Reducing False Positives in Phishing Email Detection Without Increasing False Negatives.

The main objective of this research is to investigate whether Explainable Artificial Intelligence (XAI) can help reduce false-positive phishing classifications without increasing false negatives.

I used Logistic Regression as a traditional machine learning classifier and applied SHAP explanations, counterfactual analysis, synthetic email generation, and false-negative safety screening to investigate and improve its predictions.

## 2. Dataset and Model

The experiments use the Kaggle Phishing Email Dataset (Nazario_5 and TREC07).

After data cleaning, the dataset contains 53,577 emails.

| Dataset Information | Value |
|---|---|
| Original dataset | 53,668 emails |
| Cleaned dataset | 53,577 emails |
| Training samples | 42,861 |
| Testing samples | 10,716 |
| Feature extraction | TF-IDF |
| Maximum TF-IDF features | 10,000 |
| N-gram range | Unigrams and bigrams |
| Classification model | Logistic Regression |
| Explainability method | SHAP |

The dataset contains legitimate and phishing emails. I used stratified training and testing splits to preserve the class distribution.

## 3. GitHub Project Structure

The Logistic Regression experiments are organized into two main folders: `notebooks/` and `results/`.

```text
logistic_regression/
│
├── README.md
│
├── notebooks/
│   ├── 00_environment_setup.ipynb
│   ├── 01_dataset_preparation.ipynb
│   ├── 02_official_baseline.ipynb
│   ├── 03_shap_error_analysis.ipynb
│   ├── 04_counterfactual.ipynb
│   ├── 05_synthetic_variant_generation.ipynb
│   ├── 06_fn_safety_screening.ipynb
│   └── 07_final_model_thesis.ipynb
│
└── results/
    ├── README.md
    │
    ├── figures/
    │   ├── README.md
    │   ├── baseline_confusion_matrix.png
    │   ├── baseline_fp_fn_bar_chart.png
    │   ├── roc_curve.png
    │   ├── Shap_confusion_matrix.png
    │   ├── Shap_Histogram.png
    │   ├── Shap_Roc_Curve.png
    │   └── Shap_Table_comparison.png
    │
    ├── metrics/
    │   ├── baseline_metrics.json
    │   └── final_metrics.json
    │
    └── tables/
        ├── baseline_performance.csv
        ├── baseline_performance_table.png
        └── Shap_Table_comparison.png
```

This structure reflects the files currently included in the repository snapshot.

## 4. Experimental Workflow

The experiments were organized into eight notebooks, following the research process from environment preparation to final evaluation.

| Notebook | Description |
|---|---|
| [00 — Environment Setup](notebooks/00_environment_setup.ipynb) | Prepares the Python environment and required libraries. |
| [01 — Dataset Preparation](notebooks/01_dataset_preparation.ipynb) | Loads, cleans, and prepares the phishing email dataset. |
| [02 — Official Baseline](notebooks/02_official_baseline.ipynb) | Trains and evaluates the original Logistic Regression classifier. |
| [03 — SHAP Error Analysis](notebooks/03_shap_error_analysis.ipynb) | Uses SHAP to investigate features associated with classification errors. |
| [04 — Counterfactual Analysis](notebooks/04_counterfactual.ipynb) | Investigates feature modifications that could correct false-positive predictions. |
| [05 — Synthetic Variant Generation](notebooks/05_synthetic_variant_generation.ipynb) | Generates candidate email variants based on the analysis. |
| [06 — FN Safety Screening](notebooks/06_fn_safety_screening.ipynb) | Evaluates candidate interventions to reduce the risk of increasing false negatives. |
| [07 — Final Model](notebooks/07_final_model_thesis.ipynb) | Evaluates the final model and compares its performance with the baseline. |

Each notebook documents a different stage of the experiment, allowing readers to follow the methodology in order.

## 5. Explainability-Guided Methodology

The experimental workflow consists of five main stages.

**Stage 1 — Baseline Classification**

I trained the Logistic Regression model using TF-IDF features and evaluated its performance on the held-out test dataset.

**Stage 2 — SHAP Error Analysis**

I applied SHAP to investigate the features contributing to incorrect predictions, particularly legitimate emails incorrectly classified as phishing.

**Stage 3 — Counterfactual Analysis**

I investigated feature changes that could help correct false-positive classifications.

**Stage 4 — Synthetic Variants and Safety Screening**

I generated candidate synthetic email variants and screened the proposed interventions to reduce the risk of increasing false negatives.

**Stage 5 — Final Evaluation**

I evaluated the final model against the official baseline to determine whether the intervention achieved the research objective.

## 6. Main Experimental Findings

The recorded evaluation results show a reduction in false-positive classifications without an increase in false negatives.

| Evaluation Measure | Official Baseline | Final Model |
|---|---:|---:|
| Accuracy | 98.73% | 98.81% |
| False Positives | 88 | 79 |
| False Negatives | 48 | 48 |
| Total Test Samples | 10,716 | 10,716 |

The number of false positives decreased by **9**, representing a **10.23% reduction**.

False negatives remained unchanged at **48**.

These findings indicate that the explainability-guided intervention achieved the main experimental objective on the held-out test set.

Detailed evaluation metrics, confusion matrices, and visualizations are available in the [Results folder](results/).

## 7. Experimental Results

The `results/` directory stores the main outputs of the experiments.

**Figures:** The [figures folder](results/figures/) contains visualizations such as confusion matrices, ROC curves, SHAP analysis plots, and performance charts.

**Tables:** The [tables folder](results/tables/) contains performance summaries and comparison tables.

**Metrics:** The [metrics folder](results/metrics/) contains the recorded baseline and final evaluation metrics in JSON format.

A detailed explanation of these results is provided in the [Results README](results/README.md).

## 8. Reproducibility

The experiments are documented in Jupyter notebooks and organized according to the research workflow.

To examine or reproduce the analysis:

1. Begin with `00_environment_setup.ipynb`.
2. Continue to `01_dataset_preparation.ipynb`.
3. Follow the remaining notebooks in numerical order.
4. Consult the `results/` directory to inspect the saved evaluation outputs.

The original dataset can be obtained from the Kaggle Phishing Email Dataset (Nazario_5 and TREC07).

## 9. Conclusion

This experiment investigated how explainability-guided feature analysis could improve Logistic Regression phishing email detection.

The final model reduced false-positive classifications from 88 to 79 while maintaining the same false-negative count of 48.

The repository preserves the experimental notebooks and key evaluation results in a clear structure to support transparency, reproducibility, and further research.

[Phishing Email Dataset (Nazario_5 and TREC07)](https://www.kaggle.com/datasets/rohansood98/phishing-email-dataset-nazario-5-and-trec07)
