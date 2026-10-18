# Random Forest — Explainability-Guided Phishing Email Detection

**Master's Thesis:** *Explainability-Guided Feature Analysis for Reducing False Positives in Phishing Email Detection Without Increasing False Negatives*

## 1. Project Overview

This directory presents the complete **Random Forest (RF) experiment** conducted as part of the Master's thesis in Applied Artificial Intelligence.

The experiment investigates whether SHapley Additive exPlanations (SHAP) can support the identification and analysis of features associated with incorrect phishing email classifications, and whether these explanations can guide targeted interventions to reduce false positives without increasing false negatives.

The Random Forest experiment was developed and evaluated independently of the Logistic Regression and DistilBERT experiments. Although the models use the same source dataset, each has its own experimental pipeline, baseline, explainability analysis, intervention, and evaluation.

The workflow covers environment verification, dataset preparation, baseline modelling, SHAP analysis, feature screening, counterfactual discovery, synthetic text reconstruction, targeted retraining, and final evaluation.

### Research Objective

The main objective is to reduce the number of legitimate emails incorrectly classified as phishing while ensuring that the number of phishing emails incorrectly classified as legitimate does not increase.

The predefined experimental success criterion is:

- **Baseline false positives (FP):** 62
- **Baseline false negatives (FN):** 15
- **Required improvement:** FP < 62 and FN ≤ 15

---

## 2. Repository Structure

The Random Forest directory is organized into three main components: experimental notebooks, exported results, and project documentation.

```text
random_forest/
│
├── README.md
│
├── notebooks/
│   └── rf_notebook/
│       ├── rf_00_environment_setup.ipynb
│       ├── rf_01_dataset_preparation.ipynb
│       ├── rf_02_official_baseline.ipynb
│       ├── rf_03_shap_error_analysis.ipynb
│       ├── rf_04_shap_guided_feature_analysis_counterfactual_discovery.ipynb
│       ├── rf_05_counterfactual_text_reconstruction_final.ipynb
│       ├── rf_06_final_shap_guided_random_forest_polished.ipynb
│       └── rf_07_random_forest_experimental_summary_final.ipynb
│
└── results/
    ├── figures/
    │   ├── rf_baseline_confusion_matrix.png
    │   ├── rf_baseline_fp_fn_comparison.png
    │   ├── rf_baseline_roc_curve.png
    │   ├── rf_shap_confusion_matrix.png
    │   ├── rf_shap_fp_fn_histogram.png
    │   ├── rf_shap_roc_curve.png
    │   └── rf_shap_fp_histogram.png
    │
    ├── metrics/
    │   ├── rf_baseline_metrics.json
    │   ├── rf_shap_metrics.json
    │   └── rf_shap_guided_improvement.json
    │
    └── tables/
        ├── rf_baseline_performance.csv
        ├── rf_baseline_performance_table.png
        └── [SHAP analysis tables]
```

**Note:** The structure documents the intended organization and principal output filenames. Actual committed filenames should be checked against the GitHub repository. Additional figures, tables, and `.gitkeep` files may be present.

### Experimental Notebook Guide

| Notebook | Description |
|---|---|
| **RF 00 — Environment Setup** | Verifies the Python environment, required libraries, and execution setup. |
| **RF 01 — Dataset Preparation** | Downloads, verifies, cleans, and preprocesses the dataset; prepares the stratified split and TF-IDF representation. |
| **RF 02 — Official Baseline** | Trains and evaluates the original Random Forest classifier and generates baseline performance results. |
| **RF 03 — SHAP Error Analysis** | Applies SHAP to explain all baseline false-positive and false-negative predictions. |
| **RF 04 — SHAP-Guided Feature Analysis and Counterfactual Discovery** | Screens candidate features, evaluates feature dropout, and identifies case-specific counterfactual changes. |
| **RF 05 — Counterfactual Text Reconstruction** | Reconstructs and validates synthetic legitimate email variants from selected counterfactual solutions. |
| **RF 06 — Final SHAP-Guided Evaluation** | Performs targeted weighted retraining and evaluates the updated Random Forest classifier. |
| **RF 07 — Experimental Summary** | Consolidates the complete workflow, experimental results, findings, and conclusions. |

The notebooks should be read in numerical order, from RF 00 to RF 07.

---

## 3. Dataset Description

The experiment uses the **Phishing Email Dataset (Nazario_5 and TREC07)**, available on Kaggle.

**Dataset source:** [Phishing Email Dataset — Kaggle](https://www.kaggle.com/datasets/rohansood98/phishing-email-dataset-nazario-5-and-trec07)

**Dataset file:** `email_text.csv`

### Classification Labels

| Label | Email category |
|---|---|
| 0 | Legitimate |
| 1 | Phishing |

### Dataset Statistics

| Property | Value |
|---|---:|
| Original email records | 53,668 |
| Records after cleaning | 53,577 |
| Legitimate emails | 23,739 |
| Phishing emails | 29,838 |
| Legitimate proportion | 44.31% |
| Phishing proportion | 55.69% |
| Training samples | 42,861 |
| Testing samples | 10,716 |
| Training proportion | 80% |
| Testing proportion | 20% |
| Random state | 42 |

The dataset was divided using a stratified training/testing split to preserve the approximate class distribution.

### Data Cleaning

Conservative text cleaning was applied to remove invalid or unsuitable records while preserving meaningful email content for subsequent SHAP analysis.

The cleaning process resulted in **53,577 usable emails**.

---

## 4. Feature Extraction and Model Configuration

### TF-IDF Feature Extraction

Email text was transformed into numerical features using Term Frequency–Inverse Document Frequency (TF-IDF).

The locked configuration was:

```python
TfidfVectorizer(
    max_features=10000,
    ngram_range=(1, 2),
    min_df=2,
    max_df=0.95,
    stop_words="english"
)
```

This configuration uses unigrams and bigrams, with a maximum vocabulary size of 10,000 features.

The TF-IDF vocabulary was fitted on the training data and subsequently applied to the test data.

### Random Forest Classifier

The baseline classifier used:

```python
RandomForestClassifier(
    n_estimators=100,
    random_state=42
)
```

The Random Forest model combines multiple decision trees to generate classification predictions.

The original model configuration was maintained as the reference point for evaluating subsequent interventions.

---

## 5. Official Random Forest Baseline Evaluation

The original Random Forest model was evaluated before any SHAP-guided modification.

### Baseline Confusion Matrix

Rows represent actual classes and columns represent predicted classes, using the label order `[0, 1]`.

| Actual class | Predicted legitimate (0) | Predicted phishing (1) |
|---|---:|---:|
| Legitimate (0) | 4,686 | 62 |
| Phishing (1) | 15 | 5,953 |

The baseline model produced:

- **False positives:** 62 legitimate emails classified as phishing.
- **False negatives:** 15 phishing emails classified as legitimate.

### Baseline Performance Metrics

| Evaluation metric | Result |
|---|---:|
| Accuracy | 0.99281448 |
| Precision | 0.98969244 |
| Recall | 0.99748660 |
| F1-score | 0.99357423 |
| ROC-AUC | 0.99911353 |

Precision, recall, and F1-score refer to the phishing class (label 1).

### Baseline Interpretation

The Random Forest achieved strong overall classification performance.

However, the presence of 62 false positives demonstrated that legitimate emails could still be incorrectly identified as phishing.

Since false-positive reduction is the primary research objective, these misclassified emails became the focus of the explainability-guided investigation.

---

## 6. SHAP Explainability Analysis

SHAP (SHapley Additive exPlanations) was applied to investigate how TF-IDF features contributed to the baseline Random Forest predictions.

The analysis focused on incorrect classifications.

### Misclassified Emails Analysed

| Error category | Number |
|---|---:|
| False positives | 62 |
| False negatives | 15 |
| **Total misclassified emails explained** | **77** |

All 62 false-positive and 15 false-negative cases were included in the SHAP error analysis.

SHAP was used to explain the existing classifier rather than train a separate model. Consequently, the baseline confusion matrix, classification metrics, and ROC-AUC remained unchanged during this analysis.

### Leading SHAP Features Associated with False Positives

| Rank | Feature | Mean absolute SHAP value |
|---:|---|---:|
| 1 | `wrote` | 0.02765090 |
| 2 | `org` | 0.02296829 |
| 3 | `code` | 0.02025850 |
| 4 | `escapenumber escapenumber` | 0.01361738 |
| 5 | `perl` | 0.00970759 |

These features were identified among the strongest contributors to model predictions across the analysed false-positive cases.

The results provided evidence for investigating whether particular feature patterns contributed to incorrect classification.

However, SHAP feature importance does not independently establish causality. Additional intervention experiments were therefore required.

---

## 7. SHAP-Guided Feature Screening

Following the SHAP analysis, candidate features were screened to investigate possible intervention strategies.

A total of **507 candidate features** were considered.

### Feature-Dropout Experiment

The experiment evaluated several feature-dropout levels to determine whether removing selected features could reduce false positives without increasing false negatives.

| Feature dropout | False positives | False negatives | Meets success criterion? |
|---|---:|---:|---|
| 0% (baseline) | 62 | 15 | No |
| 10% | 65 | 13 | No |
| 25% | 63 | 15 | No |
| 50% | 68 | 17 | No |
| 75% | 71 | 14 | No |

### Feature-Dropout Findings

None of the tested dropout configurations achieved the required combination of fewer than 62 false positives and no more than 15 false negatives.

Although some configurations reduced false negatives, they increased false positives.

This negative experimental result indicated that global feature removal was not sufficient for the research objective.

The investigation therefore proceeded to a more targeted counterfactual-guided approach.

---

## 8. Counterfactual Feature Analysis

Counterfactual analysis was conducted to identify targeted feature changes associated with reversing incorrect phishing predictions.

The analysis concentrated on baseline false-positive emails.

### Counterfactual Discovery Results

| Measure | Result |
|---|---:|
| Baseline false-positive cases examined | 62 |
| Cases with verified counterfactual solutions | 9 |
| Feature occurrences across solutions | 41 |
| Distinct features across solutions | 40 |
| Mean feature occurrences per solution | 4.56 |
| Median feature occurrences per solution | 3 |

Verified counterfactual solutions were obtained for nine of the 62 baseline false-positive cases.

These solutions provided a basis for reconstructing modified email texts that preserved much of the original legitimate email content.

---

## 9. Counterfactual Text Reconstruction and Validation

The feature-level counterfactual changes were translated into synthetic legitimate email variants.

### Reconstruction Results

| Measure | Value |
|---|---:|
| Validated synthetic legitimate emails | 9 |
| Mean cosine similarity | 0.99691915 |
| Minimum required cosine similarity | 0.98 |
| Original baseline classification | Phishing |

The nine validated synthetic examples retained high similarity to their source emails.

All nine source cases had been incorrectly classified as phishing by the original Random Forest baseline.

These synthetic variants were then selected for targeted retraining.

The reconstructed emails were not independent external observations; they were derived from cases identified during the experiment.

---

## 10. Final SHAP-Guided Random Forest Retraining

The final intervention incorporated the nine validated synthetic legitimate emails into the training process.

### Training Configuration

| Training component | Value |
|---|---:|
| Original training samples | 42,861 |
| Additional synthetic legitimate examples | 9 |
| Original sample weight | 1 |
| Synthetic sample weight | 7 |

The weighted training approach increased the influence of the targeted synthetic examples during model fitting.

The updated Random Forest classifier was evaluated against the original baseline.

### Final Confusion Matrix

| Actual class | Predicted legitimate (0) | Predicted phishing (1) |
|---|---:|---:|
| Legitimate (0) | 4,691 | 57 |
| Phishing (1) | 15 | 5,953 |

### Baseline and Final Error Comparison

| Evaluation measure | Baseline RF | Final RF | Change |
|---|---:|---:|---:|
| False positives | 62 | 57 | −5 |
| False negatives | 15 | 15 | 0 |
| Correctly classified legitimate emails | 4,686 | 4,691 | +5 |
| Correctly classified phishing emails | 5,953 | 5,953 | 0 |

### False-Positive Reduction

The relative false-positive reduction was calculated as:

\[
\frac{62 - 57}{62} \times 100 = 8.06\%
\]

**Final experimental outcome:**

- False positives decreased from **62 to 57**.
- The reduction was **5 false-positive cases (8.06%)**.
- False negatives remained unchanged at **15**.
- The predefined experimental error-count criterion was satisfied on the reported evaluation set.

### Evaluation Qualification

The intervention was developed using false-positive cases identified from the evaluation dataset.

Consequently, the final comparison is a **test-informed experimental result**, rather than an independently validated estimate of generalization to unseen data.

An additional untouched holdout dataset or a fully nested evaluation would be required to determine whether the observed improvement generalizes beyond the analysed cases.

This distinction is important for accurately interpreting the research findings.

---

## 11. Experimental Results and Output Files

The `results` directory stores the figures, metrics, and tables associated with the Random Forest experiment.

### 11.1 Figures

**Location:** `results/figures/`

The exported figures include:

| Figure | Description |
|---|---|
| `rf_baseline_confusion_matrix.png` | Original Random Forest baseline confusion matrix. |
| `rf_baseline_fp_fn_comparison.png` | Baseline false-positive and false-negative comparison. |
| `rf_baseline_roc_curve.png` | Baseline receiver operating characteristic curve. |
| `rf_shap_confusion_matrix.png` | Baseline confusion matrix presented during SHAP analysis. |
| `rf_shap_fp_fn_histogram.png` | False-positive and false-negative counts analysed using SHAP. |
| `rf_shap_roc_curve.png` | ROC curve for the unchanged baseline model during SHAP analysis. |
| `rf_shap_fp_histogram.png` | SHAP false-positive feature importance visualization. |

Additional final-model figures may include the final confusion matrix and baseline-versus-final error comparison.

The figures document both the original classifier's behaviour and the results of the explainability-guided investigation.

### 11.2 Metrics

**Location:** `results/metrics/`

| JSON file | Description |
|---|---|
| `rf_baseline_metrics.json` | Original Random Forest baseline metrics and confusion matrix. |
| `rf_shap_metrics.json` | Baseline metrics and SHAP analysis of all 77 misclassified emails. |
| `rf_shap_guided_improvement.json` | Baseline-to-final comparison, including the 8.06% false-positive reduction. |

These files provide machine-readable experimental results.

The SHAP metrics file describes the unchanged baseline model, whereas the improvement file records the effect of the later targeted intervention.

### 11.3 Tables

**Location:** `results/tables/`

The tables directory contains numerical summaries and formatted tables used to document the experimental findings.

These include:

- `rf_baseline_performance.csv` — baseline performance metrics.
- `rf_baseline_performance_table.png` — formatted baseline performance table.
- SHAP feature-analysis tables — ranked explanatory features and associated values.
- Other exported tables documenting the experimental analysis, where available.

The numerical results in the tables should remain consistent with the corresponding notebooks and JSON metrics files.

---

## 12. Experimental Workflow and Reproducibility

The experimental notebooks are organized sequentially to document the full research process.

The recommended reading order is:

1. **RF 00:** Verify the computational environment.
2. **RF 01:** Prepare the dataset and feature representation.
3. **RF 02:** Establish the official Random Forest baseline.
4. **RF 03:** Analyse baseline classification errors using SHAP.
5. **RF 04:** Evaluate feature-screening and counterfactual-discovery strategies.
6. **RF 05:** Reconstruct and validate synthetic legitimate email variants.
7. **RF 06:** Perform targeted retraining and final evaluation.
8. **RF 07:** Review the consolidated experimental findings.

### Reproducibility Considerations

Reproducing the reported experiment requires preserving the documented:

- Dataset cleaning procedure and class definitions.
- Stratified training/testing split and random seed.
- TF-IDF vocabulary configuration.
- Random Forest baseline configuration.
- SHAP analysis and feature-screening procedure.
- Counterfactual reconstruction and validation criteria.
- Synthetic-example weighting.
- Evaluation metrics and confusion-matrix label order.

The notebook files contain the corresponding experimental code and recorded outputs.

Because the final intervention uses information derived from test-set errors, a separate untouched evaluation set would be necessary for independent validation of the reported improvement.

---

## 13. Main Experimental Findings

The Random Forest experiment produced five principal findings.

**First**, the original Random Forest achieved strong classification performance, with an accuracy of approximately 99.28% and ROC-AUC of approximately 0.9991.

**Second**, SHAP provided feature-level explanations for all 62 false-positive and 15 false-negative baseline cases, identifying patterns associated with incorrect predictions.

**Third**, the feature-dropout experiments did not satisfy the joint requirement of reducing false positives while preventing an increase in false negatives.

**Fourth**, case-specific counterfactual analysis identified nine verified solutions, which supported the construction of nine validated synthetic legitimate email variants.

**Fifth**, the targeted weighted retraining experiment reduced the observed false positives from 62 to 57, representing an **8.06% reduction**, while maintaining the original 15 false negatives.

The final finding is exploratory because the intervention was informed by errors observed on the evaluation set. Its generalization to independently unseen emails remains to be established.

---

## 14. Research Contribution

This Random Forest experiment contributes to the broader investigation of explainability-guided phishing email detection by examining whether model explanations can support targeted correction of false-positive predictions.

Its contribution includes:

- Establishing an independently evaluated Random Forest baseline.
- Using SHAP to analyse both false-positive and false-negative errors.
- Evaluating and documenting unsuccessful global feature-dropout interventions.
- Investigating case-specific counterfactual feature changes.
- Reconstructing and validating targeted synthetic legitimate emails.
- Demonstrating a reduction in observed false positives without an increase in false negatives under the reported evaluation conditions.

The experiment illustrates how explainability methods can inform targeted model-improvement strategies while also highlighting the importance of independent evaluation when measuring their effectiveness.

---

## 15. Relationship to the Complete Thesis

The Random Forest experiment forms one independent component of the thesis:

**Explainability-Guided Feature Analysis for Reducing False Positives in Phishing Email Detection Without Increasing False Negatives.**

The complete research also includes separate Logistic Regression and DistilBERT experiments.

Each model has its own preprocessing, training, baseline evaluation, explainability analysis, intervention strategy, and results.

The Random Forest findings are therefore reported independently and should not be interpreted as improvements obtained by transferring results from another classifier.

---

## 16. Conclusion

The Random Forest experiment investigated the use of SHAP explanations and counterfactual-guided feature analysis to address false-positive errors in phishing email detection.

The original classifier produced 62 false positives and 15 false negatives. Global feature-dropout experiments did not achieve the required improvement.

A subsequent targeted intervention, using nine validated synthetic legitimate email variants and weighted retraining, reduced the observed false positives to 57 while keeping false negatives unchanged at 15.

This corresponds to an **8.06% reduction in false positives** on the reported evaluation set.

Although independent generalization remains to be established, the experiment provides evidence that SHAP-guided counterfactual analysis can support targeted investigation and correction of classification errors.

The complete workflow, from environment verification to final experimental conclusions, is documented in the eight Random Forest notebooks and their associated results files.
