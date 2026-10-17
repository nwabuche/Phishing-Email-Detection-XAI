# Official Logistic Regression Baseline Results

## 1. Purpose

This document presents the locked Logistic Regression baseline for the phishing email detection study. It provides the reference against which subsequent explainability-guided corrective retraining is evaluated.

## 2. Dataset and experimental configuration

After cleaning, the dataset contained **53,577 emails**, divided using a fixed, stratified 80/20 train–test split: **42,861 training emails** and **10,716 test emails**. Legitimate emails were assigned label **0**, and phishing emails label **1**.

| Component | Setting |
|---|---|
| Text representation | Term Frequency–Inverse Document Frequency (TF-IDF) |
| Maximum features | 10,000 |
| N-gram range | `(1, 2)` (unigrams and bigrams) |
| Minimum document frequency | `min_df=2` |
| Maximum document frequency | `max_df=0.95` |
| Stop words | English |
| Vectorizer fitting | Training partition only |
| Classifier | Logistic Regression |
| Random state | `42` |
| Maximum iterations | `1000` |
| Classification threshold | `0.50` |

## 3. Baseline evaluation

| Metric | Recorded result |
|---|---:|
| Test emails | 10,716 |
| Accuracy | 98.7309% |
| Precision (phishing, label 1) | 98.5353% |
| Recall (phishing, label 1) | 99.1957% |
| F1-score (phishing, label 1) | 98.8644% |
| ROC-AUC (phishing, label 1) | 0.998767 |
| False Positives (legitimate classified as phishing) | 88 |
| False Negatives (phishing classified as legitimate) | 48 |

**Confusion matrix** (actual labels in rows; predicted labels in columns):

| Actual / Predicted | Legitimate (0) | Phishing (1) | Total |
|---|---:|---:|---:|
| Legitimate (0) | 4,660 | 88 | 4,748 |
| Phishing (1) | 48 | 5,920 | 5,968 |
| **Total** | **4,708** | **6,008** | **10,716** |

**Reporting convention.** The thesis refers to the 4,660 correctly classified legitimate emails as *True Positives* and the 5,920 correctly classified phishing emails as *True Negatives*. In scikit-learn's standard label-1-positive convention, those two diagonal counts are termed *true negatives* and *true positives*, respectively. In both conventions, the study's **88 False Positives** are legitimate emails flagged as phishing, and its **48 False Negatives** are phishing emails classified as legitimate. The precision, recall, F1-score, and ROC-AUC above use phishing (label 1) as the positive class.

## 4. Interpretation

The baseline correctly classified **10,580** test emails and misclassified **136**. Its **88 False Positives** provide the cases for subsequent SHAP-based error analysis and counterfactual investigation. The **48 False Negatives** establish the safety reference for assessing whether corrective retraining compromises phishing detection. These baseline results alone do not establish the effectiveness of the later intervention.

## 5. Reproducibility and provenance

The results are taken from the original locked **Step 6** experiment, documented in [`02_official_baseline.ipynb`](../../notebooks/02_official_baseline.ipynb). The experiment specifies the Google Drive directory `LR_Official_Baseline_LOCKED` for saving the fitted model, TF-IDF vectorizer, predictions, error records, configuration, and integrity hashes. Its recorded output filenames include:

- `official_baseline_test_predictions.csv`
- `official_baseline_false_positives.csv`
- `official_baseline_false_negatives.csv`
- `official_baseline_metrics_and_config.json`
- `baseline_integrity_hashes.json`

**Verification scope.** Accuracy, precision, recall, and F1-score were recalculated from the reported confusion matrix. ROC-AUC is the value recorded by the original experiment and cannot be verified from the confusion matrix alone. The original per-email prediction files have not been independently retrieved or re-executed for this document; no individual prediction records have been fabricated.
