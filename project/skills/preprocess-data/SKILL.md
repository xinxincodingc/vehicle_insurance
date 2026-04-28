---
name: preprocess-data
description: "A skill to clean and transform raw fraud_oracle.csv by dropping PolicyNumber/RepNumber, encoding categoricals, applying SMOTE/class weights, and producing a 70/15/15 train/validation/test split"
---

## When to use
This is the first node in the pipeline. It is triggered once at the start of 
every pipeline run before any model training or inference occurs.

## How to execute
1. Load fraud_oracle.csv into a pandas DataFrame
2. Drop identifier columns: PolicyNumber, RepNumber
3. Separate features (X) and label (y = FraudFound_P)
4. Perform stratified 70/15/15 split into train, val, test sets
5. One-hot encode all object/string columns on the training set,
   then apply the same encoder to val and test (fit on train only)
6. Apply SMOTE to the training set only to address the 6% fraud class imbalance
7. Store all splits and the fitted encoder in agent state

## Inputs from agent state
- raw_data_path: str — file path to fraud_oracle.csv

## Outputs to agent state
- X_train: pd.DataFrame — SMOTE-resampled training features
- y_train: pd.Series — SMOTE-resampled training labels
- X_val: pd.DataFrame — validation features (no resampling)
- y_val: pd.Series — validation labels
- X_test: pd.DataFrame — held-out test features (no resampling)
- y_test: pd.Series — held-out test labels
- encoder: OneHotEncoder — fitted on training data, applied to val and test
- feature_names: list[str] — column names after encoding

## Output format
Six split arrays and one fitted encoder object, all stored as named fields 
in AgentState. Shapes: X_train will be larger than original 70% split due 
to SMOTE oversampling of the minority class.

## Notes
- SMOTE must never be applied to val or test splits. Doing so causes data 
  leakage and artificially inflates recall on evaluation.
- The encoder must be fitted on the training set only, then transformed 
  applied to val and test. Fitting on the full dataset leaks test distribution.
- FraudFound_P is approximately 6% positive (923/15420). Without handling 
  this imbalance, models will learn to predict "not fraud" for everything 
  and achieve misleading 94% accuracy.
- random_state=42 must be set on both the train/test split and SMOTE 
  for reproducibility.
