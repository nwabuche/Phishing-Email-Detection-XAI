# Logistic Regression.
# Logistic Regression — Experimental Results

## Overview

This folder contains the experimental results of the Logistic Regression model developed for my master's thesis:

**Explainability-Guided Feature Analysis for Reducing False Positives in Phishing Email Detection Without Increasing False Negatives.**

The purpose of this folder is to organize and preserve the evaluation results, performance metrics, tables, and figures produced during my experiments.

My main objective is to reduce false-positive phishing classifications while ensuring that false negatives do not increase.

## Folder Structure

```text
result/
├── README.md
├── metrics/
│   ├── baseline_metrics.json
│   └── final_metrics.json
├── figures/
└── tables/
```

The structure above represents the planned organization. Files will be added as the results are prepared and verified.

## Baseline and Final Model Results

The following table summarizes the recorded classification results.

| Evaluation Metric | Baseline Model | Final Model |
|---|---:|---:|
| True Negatives (TN) | 4,660 | 4,669 |
| False Positives (FP) | 88 | 79 |
| False Negatives (FN) | 48 | 48 |
| True Positives (TP) | 5,920 | 5,920 |
| Total Test Samples | 10,716 | 10,716 |

The confusion matrices use the standard scikit-learn class ordering, where legitimate emails are labelled 0 and phishing emails are labelled 1.

## Key Findings

The final Logistic Regression model achieved the following results:

- False positives decreased from 88 to 79.
- Nine false-positive classifications were eliminated.
- The false-positive count decreased by approximately 10.23%.
- False negatives remained unchanged at 48.
- The total number of correctly classified emails increased by nine.

These results show that my explainability-guided intervention reduced false-positive errors without increasing false negatives on the held-out test set.

## Description of Result Files

### Metrics

The `metrics/` folder stores the evaluation results in JSON format.

- `baseline_metrics.json` — Performance results before the explainability-guided intervention.
- `final_metrics.json` — Performance results after the intervention.

### Figures

The `figures/` folder is intended to contain visual comparisons, including confusion matrices, false-positive and false-negative comparisons, and ROC curves where the necessary evaluation data is available.

### Tables

The `tables/` folder is intended to contain summary tables presenting the model's evaluation results and comparisons.

## Reproducibility

The corresponding Jupyter notebooks are available in the `logistic_regression/notebooks/` directory.

These notebooks document the dataset preparation, baseline training, SHAP analysis, counterfactual analysis, synthetic variant generation, false-negative safety screening, and final model evaluation.

The JSON files and other saved results provide a convenient way to inspect the experimental outcomes without rerunning the complete training pipeline.

## Conclusion

My experiments demonstrate that explainability-guided feature analysis can help reduce false-positive classifications in phishing email detection while preserving the observed false-negative count.

This folder provides an organized record of the results supporting that conclusion.


