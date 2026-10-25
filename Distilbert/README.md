# DistilBERT

SHAP-Guided Phishing Email Detection Using DistilBERT
# DistilBERT — SHAP-Guided Phishing Email Detection

**Master's research project | Applied Artificial Intelligence | Polytechnic Institute of Cávado and Ave (IPCA), Portugal**

**Thesis:** *Explainability-Guided Feature Analysis for Reducing False Positives in Phishing Email Detection Without Increasing False Negatives*

## 1. Project overview

Phishing emails attempt to deceive recipients into disclosing information, opening malicious links, or taking other unsafe actions. Automated detection can help identify these messages, but classification errors remain important. A **false positive** incorrectly flags a legitimate email as phishing, potentially interrupting normal communication. A **false negative** incorrectly allows a phishing email to be classified as legitimate, creating a security risk.

This project investigates whether **Explainable Artificial Intelligence (XAI)** can help identify and correct error patterns in a fine-tuned **DistilBERT** phishing email classifier. I used **SHAP (SHapley Additive exPlanations)** to examine influential text features in misclassified emails, tested several controlled refinement approaches, and evaluated a final corrective fine-tuning strategy designed to reduce false positives while protecting phishing detection performance.

The DistilBERT experiment is an **independent model investigation** within my master's thesis. Logistic Regression and Random Forest are documented separately in the same repository; the results presented here are specific to DistilBERT.

### Research question

> Can SHAP-guided analysis and targeted corrective refinement reduce false-positive phishing classifications without increasing false negatives?

### Main contributions

- Established a fine-tuned DistilBERT baseline on a consistent, stratified email dataset split.
- Investigated false-positive and false-negative predictions with SHAP-based text explanations.
- Tested multiple refinement strategies and documented their effects on both error types.
- Developed targeted corrective examples with false-negative safety considerations.
- Evaluated a final corrective fine-tuning stage against the original baseline using the same test set.
- Organized the experimental workflow, performance metrics, figures, and tables for review.

## 2. Dataset

The experiment uses the **Phishing Email Dataset (Nazario_5 and TREC07)**, distributed through Kaggle. The working source file is `email_text.csv`.

| Dataset property | Recorded value |
|:---|---:|
| Original records | 53,668 |
| Records after cleaning | 53,577 |
| Training records | 42,861 |
| Test records | 10,716 |
| Legitimate emails in the test set | 4,748 |
| Phishing emails in the test set | 5,968 |
| Training/test allocation | 80% / 20% |
| Split method | Stratified |
| Random seed | 42 |

**Class labels:** `0 = Legitimate`, `1 = Phishing`.

The dataset was cleaned before splitting, and the stratified split preserved the relative distribution of legitimate and phishing emails. The same held-out test partition was used to report the recorded baseline and final evaluation results.

## 3. DistilBERT baseline

The baseline uses a pretrained **`distilbert-base-uncased`** transformer adapted for binary email classification.

| Training configuration | Baseline setting |
|:---|:---|
| Model | `distilbert-base-uncased` |
| Task | Binary text classification |
| Input length | Up to 256 tokens |
| Batch size | 8 |
| Optimizer | AdamW |
| Learning rate | 2 × 10⁻⁵ |
| Training epochs | 2 |
| Random seed | 42 |
| Compute used | Google Colab with NVIDIA T4 GPU |

The baseline provides a reference for evaluating whether subsequent explanation-guided interventions improve classification behavior.

### Baseline evaluation

| Metric | Baseline DistilBERT |
|:---|---:|
| Accuracy | 0.995054 |
| Precision | 0.994813 |
| Recall | 0.996314 |
| F1-score | 0.995563 |
| ROC-AUC | 0.999262 |
| False positives | 31 |
| False negatives | 22 |
| Total classification errors | 53 |

**Baseline confusion matrix** (rows: actual labels; columns: predicted labels):

| Actual class | Predicted legitimate (0) | Predicted phishing (1) |
|:---|---:|---:|
| Legitimate (0) | 4,717 | 31 |
| Phishing (1) | 22 | 5,946 |

For clarity, **precision, recall, F1-score, and ROC-AUC are reported for the phishing class (`1`)**. The matrix explicitly shows the actual and predicted classes so that its interpretation does not depend on naming conventions for true positives and true negatives.

## 4. Explainability and error analysis

I used **SHAP** to investigate which words and text fragments contributed to DistilBERT's predictions, particularly in emails classified incorrectly. The analysis used SHAP's text masking and partition-based explanation approach, with attention to the phishing prediction class.

The explanation workflow focused on two types of mistakes:

- **False positives:** legitimate emails incorrectly classified as phishing.
- **False negatives:** phishing emails incorrectly classified as legitimate.

SHAP helps interpret the model's decisions; **running SHAP does not itself retrain the classifier or change its predictions**. The baseline's 31 false positives and 22 false negatives therefore remain the reference counts for explanation of the unchanged baseline.

The SHAP investigation informed later experiments in which corrective strategies were tested separately. This distinction is important: explanations identify potentially useful evidence, while improvements must be demonstrated through subsequent model evaluation.

## 5. Experimental workflow

The work is documented in **11 sequential notebooks**, from environment setup to final evaluation.

| Notebook | Purpose |
|:---|:---|
| `bert_00_environment_setup.ipynb` | Prepare and verify the Python, library, and execution environment. |
| `bert_01_dataset_preparation.ipynb` | Load, clean, label, and split the phishing email dataset. |
| `bert_02_official_baseline.ipynb` | Fine-tune DistilBERT and establish the official baseline. |
| `bert_03_shap_error_analysis.ipynb` | Examine misclassified emails with SHAP text explanations. |
| `bert_04_shap_guided_attribution_regularization.ipynb` | Test attribution-regularized refinement informed by explanation results. |
| `bert_05_controlled_shap_refinement.ipynb` | Assess a controlled SHAP-guided refinement strategy. |
| `bert_06_shap_guided_training_refinement.ipynb` | Examine an alternative training-example refinement strategy. |
| `bert_07_shap_counterfactual_selective_correction.ipynb` | Investigate targeted counterfactual corrections and their error trade-offs. |
| `bert_08_shap_guided_corrective_example_selection.ipynb` | Generate, recover, screen, and select corrective examples. |
| `bert_09_safety_preserved_corrective_fine_tuning.ipynb` | Fine-tune with selected legitimate examples and phishing replay examples. |
| `bert_10_final_evaluation_and_results.ipynb` | Consolidate final metrics, error counts, comparisons, and visual evidence. |

The intermediate notebooks document **investigated approaches**, not a claim that every approach improved the model. In particular, some candidate refinements reduced false positives while increasing false negatives, which did not satisfy the project's safety objective.

### Selected intermediate findings

| Experimental stage | False positives | False negatives | Interpretation |
|:---|---:|---:|:---|
| Official baseline | 31 | 22 | Reference model |
| Attribution regularization | 36 | 21 | Fewer false negatives, but more false positives |
| Controlled refinement | 28 | 27 | Fewer false positives, but more false negatives |
| Weighted refinement | 29 | 24 | Fewer false positives, but more false negatives |
| Selective counterfactual correction | 23 | 22 | Lower false-positive count without a higher false-negative count in that evaluation |
| Final corrective fine-tuning | 26 | 17 | Both error types lower than the baseline |

These stages should not be interpreted as one continuously improving model. Each intervention was evaluated according to its own procedure, and the final reported model comes from the corrective fine-tuning workflow.

## 6. SHAP-guided corrective example selection

The corrective-example workflow used error analysis and targeted text changes to construct candidate legitimate-email examples intended to address recurring false-positive patterns. Candidates were filtered before use in training.

| Corrective-example selection stage | Count |
|:---|---:|
| Generated candidates | 129 |
| Recovered candidates | 128 |
| Accepted candidates | 26 |
| Final selected legitimate examples | 21 |

The final training intervention paired the **21 selected synthetic legitimate examples** with **21 phishing replay examples**. The phishing replay examples were included to help preserve sensitivity to phishing emails during corrective fine-tuning.

| Final fine-tuning dataset component | Number of emails |
|:---|---:|
| Original training records | 42,861 |
| Selected corrective legitimate examples | 21 |
| Phishing replay examples | 21 |
| **Total training examples** | **42,903** |

The corrective fine-tuning stage used **one epoch** and a **learning rate of 1 × 10⁻⁵**. These additions were part of the training workflow, not additional records in the held-out test set.

## 7. Final results

### Baseline versus SHAP-guided final model

| Evaluation metric | Baseline DistilBERT | SHAP-guided final DistilBERT |
|:---|---:|---:|
| Accuracy | 0.995054 | **0.995987** |
| Precision | 0.994813 | **0.995650** |
| Recall | 0.996314 | **0.997151** |
| F1-score | 0.995563 | **0.996400** |
| ROC-AUC | 0.999262 | **0.999384** |
| Correctly classified legitimate emails | 4,717 | **4,722** |
| False positives | 31 | **26** |
| False negatives | 22 | **17** |
| Correctly classified phishing emails | 5,946 | **5,951** |
| Total classification errors | 53 | **43** |

**Final confusion matrix** (rows: actual labels; columns: predicted labels):

| Actual class | Predicted legitimate (0) | Predicted phishing (1) |
|:---|---:|---:|
| Legitimate (0) | 4,722 | 26 |
| Phishing (1) | 17 | 5,951 |

### Error reductions

| Error measure | Baseline | Final | Absolute reduction | Relative reduction |
|:---|---:|---:|---:|---:|
| False positives | 31 | 26 | 5 | **16.13%** |
| False negatives | 22 | 17 | 5 | **22.73%** |
| Total errors | 53 | 43 | 10 | **18.87%** |

The final model **met the stated experimental objective** on the recorded test evaluation: false positives decreased from 31 to 26, and false negatives decreased rather than increasing. Accuracy, precision, recall, F1-score, and ROC-AUC also improved.

The main contribution is not simply a high accuracy score. It is the documented process of examining errors, testing corrective interventions, and checking whether a reduction in false positives compromises the detection of phishing emails.

## 8. Repository organization

```text
Distilbert/
├── README.md
├── notebooks/
│   ├── bert_00_environment_setup.ipynb
│   ├── bert_01_dataset_preparation.ipynb
│   ├── bert_02_official_baseline.ipynb
│   ├── bert_03_shap_error_analysis.ipynb
│   ├── bert_04_shap_guided_attribution_regularization.ipynb
│   ├── bert_05_controlled_shap_refinement.ipynb
│   ├── bert_06_shap_guided_training_refinement.ipynb
│   ├── bert_07_shap_counterfactual_selective_correction.ipynb
│   ├── bert_08_shap_guided_corrective_example_selection.ipynb
│   ├── bert_09_safety_preserved_corrective_fine_tuning.ipynb
│   └── bert_10_final_evaluation_and_results.ipynb
└── results/
    ├── README.md
    ├── figures/
    │   ├── README.md
    │   └── [experimental confusion matrices, FP/FN charts, ROC curves, and SHAP figures]
    ├── metrics/
    │   ├── README.md
    │   ├── distilbert_baseline_metrics.csv
    │   ├── distilbert_shap_guided_final_metrics.csv
    │   └── distilbert_baseline_vs_shap_guided_comparison.csv
    └── tables/
        ├── README.md
        └── [formatted experimental performance and comparison tables]
```

**Browse the work:** [Experiment notebooks](notebooks/) · [Figures](results/figures/) · [Metrics](results/metrics/) · [Tables](results/tables/)

The notebook filenames above describe the reviewed sequence. Figures and tables are listed by category because their exact uploaded filenames may vary.

## 9. Tools and methods

| Area | Tools or methods |
|:---|:---|
| Programming | Python |
| Experiment environment | Google Colab |
| Deep learning framework | PyTorch |
| Transformer model | Hugging Face DistilBERT (`distilbert-base-uncased`) |
| Explainable AI | SHAP, text masking, partition-based explanations |
| Model evaluation | Accuracy, precision, recall, F1-score, ROC-AUC, confusion matrix |
| Visualization | Confusion matrices, FP/FN bar charts, ROC curves, SHAP feature plots |
| Data handling | Stratified training/test split and dataset cleaning |
| Corrective development | Counterfactual text analysis, candidate screening, targeted fine-tuning |

## 10. How to review or reproduce the experiments

1. **Start with the environment notebook:** Open [`bert_00_environment_setup.ipynb`](notebooks/bert_00_environment_setup.ipynb) in Google Colab and follow its setup instructions.
2. **Obtain the source dataset:** Download the Kaggle *Phishing Email Dataset (Nazario_5 and TREC07)* and make `email_text.csv` available at the path expected by the dataset notebook.
3. **Prepare the dataset:** Follow [`bert_01_dataset_preparation.ipynb`](notebooks/bert_01_dataset_preparation.ipynb), keeping the recorded cleaning and stratified split settings.
4. **Review the baseline:** Use [`bert_02_official_baseline.ipynb`](notebooks/bert_02_official_baseline.ipynb) to inspect the baseline configuration and results.
5. **Inspect explanations and intervention stages:** Work through notebooks 03–09 in numerical order, checking each notebook's input files and model dependencies.
6. **Review the final evaluation:** Open [`bert_10_final_evaluation_and_results.ipynb`](notebooks/bert_10_final_evaluation_and_results.ipynb) and compare its reported values with the files in [`results/`](results/).

**Reproduction note:** The notebooks document the research workflow, but some later stages depend on intermediate outputs, model checkpoints, or per-email prediction files produced during the original Google Colab experiments. The final evaluation notebook may require its original per-email evaluation CSV to redraw the ROC curve from prediction scores. Check the input requirements within each notebook before executing it. The README does not claim that every figure can be regenerated from the summary metrics alone.

## 11. Interpretation and scope

The results show that a carefully selected corrective fine-tuning intervention **can reduce both false positives and false negatives in the recorded experiment**. They also show why it is important to evaluate both error types: several intermediate interventions lowered false positives but increased false negatives.

The intervention process used information obtained through model error analysis, including analysis of held-out predictions. Consequently, the final test comparison should be understood as an **experimental, test-informed refinement result**, rather than as proof of performance on a completely untouched external dataset. A further independent validation set or external email corpus would be valuable before making claims about deployment performance.

This project demonstrates practical skills in transformer fine-tuning, explainability, error analysis, controlled experimentation, synthetic-example screening, and communicating model performance with reproducible research artifacts.

## 12. Research context

This DistilBERT investigation forms part of my master's research in **Applied Artificial Intelligence** at the **Polytechnic Institute of Cávado and Ave (IPCA), Portugal**.

**Thesis title:** *Explainability-Guided Feature Analysis for Reducing False Positives in Phishing Email Detection Without Increasing False Negatives*.

The broader repository also contains separate Logistic Regression and Random Forest experiments. Their findings should be interpreted independently, even though the studies share a common research question and dataset.
