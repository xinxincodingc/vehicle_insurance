---
name: train-models
description: "A skill to train three classifiers on the preprocessed fraud dataset: Model A (Logistic Regression), Model B (Random Forest), and Model C (XGBoost)"
---

## When to use
This skill is triggered after the preprocess-data skill has completed and the training/validation/test splits are stored in agent state. It trains three different classifiers on the training data and evaluates them on the validation set to select the best model for final testing.

## How to execute
1. Retrieve the preprocessed data splits (X_train, y_train, X_val, y_val, X_test, y_test) and the fitted encoder from agent state.
2. Train Model A (Logistic Regression) on the training set and evaluate on the validation set.
3. Train Model B (Random Forest) on the training set and evaluate on the validation set.
4. Train Model C (XGBoost) on the training set and evaluate on the validation set.
5. Compare the evaluation metrics (e.g., AUC-ROC, Recall) of all three models and select the best one based on the defined criteria.
6. Document the selected model and the justification for its selection in the shared agent state.

## Inputs from agent state
- X_train: pd.DataFrame — training features
- y_train: pd.Series — training labels
- X_val: pd.DataFrame — validation features
- y_val: pd.Series — validation labels
- X_test: pd.DataFrame — test features
- y_test: pd.Series — test labels
- encoder: OneHotEncoder — fitted on training data

## Outputs to agent state
- best_model: str — name of the selected model (A, B, or C)
- model_metrics: dict — evaluation metrics of the selected model
- model_justification: str — justification for model selection

## Output format
The output should include the name of the best model, its evaluation metrics, and a justification for its selection, all stored as named fields in AgentState.