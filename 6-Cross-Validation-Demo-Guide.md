# Instructor Guide — Cross-Validation and Model Selection

**Audience:** Intermediate professionals  
**Duration:** 75–90 minutes  
**Environment:** Amazon SageMaker AI Studio → JupyterLab → Python 3 kernel

## Files
- `SageMaker_Cross_Validation_Model_Selection.ipynb`
- `wdbc_dataset.csv`

## Setup
1. Open SageMaker Studio and launch a JupyterLab space.
2. Select a CPU instance suitable for scikit-learn experimentation.
3. Upload both files into the same working directory.
4. Open the notebook and run cells in sequence.
5. The first code cell installs the required packages.

## Teaching schedule
| Topic | Time |
|---|---:|
| Dataset inspection and target | 8 min |
| Hold-out test split | 5 min |
| Stratified 5-fold CV | 15 min |
| Model comparison and leakage-safe pipelines | 10 min |
| Randomized hyperparameter search | 15 min |
| Final test evaluation and metrics | 10 min |
| Interpretation, persistence and discussion | 12 min |

## Core concepts
- **K-fold CV:** train on K-1 folds and validate on the remaining fold, repeating K times.
- **Stratification:** preserves approximate class proportions in each fold.
- **Mean and standard deviation:** mean summarizes expected performance; standard deviation indicates fold-to-fold variability.
- **Pipeline:** fits preprocessing only on the training portion of each fold, preventing leakage.
- **RandomizedSearchCV:** samples hyperparameter combinations and evaluates them with CV.
- **Hold-out test set:** reserved until model selection is complete for a final generalization estimate.
- **ROC-AUC and PR-AUC:** ranking metrics across thresholds; PR-AUC is especially useful when positive cases are relatively uncommon.
- **Nested CV:** useful when a less biased estimate of the full hyperparameter-selection process is needed.

## Discussion questions
1. Why is a single train/test split sensitive to the random split?
2. What happens if `StandardScaler` is fitted before cross-validation?
3. Why not choose the best hyperparameters using test-set performance?
4. Which model has the strongest mean ROC-AUC and which has the lowest variability?
5. What metric would you prioritize if false negatives were especially costly?
6. What changes when moving to SageMaker managed training and Pipelines?

## Runtime and cost
The notebook uses CPU-based, notebook-local scikit-learn. It does not create a SageMaker Training Job, Processing Job, Model Registry entry or endpoint. Notebook compute charges apply while the space runs; stop the space after the lab. The search uses 20 parameter combinations × 5 folds; reduce `n_iter` to 8–10 for a faster live session.

## Dataset note
The bundled Wisconsin Diagnostic Breast Cancer benchmark has 569 observations and 30 numeric predictors. `malignant` is encoded as the positive class (1). This is an educational dataset only, not for clinical use.
