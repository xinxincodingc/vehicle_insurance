---
name: run-inference
description: "A skill to take a new insurance claim as input and output a fraud probability score and confidence score using the selected model from agent state"
---

## When to use
This skill is triggered after the best model has been selected and evaluated. It is used to make predictions on new insurance claims to assess their likelihood of being fraudulent.

## How to execute
1. Retrieve the new insurance claim data and the fitted encoder from agent state.
2. Preprocess the new claim data using the fitted encoder.
3. Use the selected model to predict the fraud probability score and confidence score for the new claim.
4. Store the prediction results in agent state.

## Inputs from agent state
- new_claim_data: pd.DataFrame — new insurance claim features
- encoder: OneHotEncoder — fitted on training data

## Outputs to agent state
- fraud_probability: float — predicted fraud probability score for the new claim
- confidence_score: float — confidence score for the fraud prediction

## Output format
The output should include the fraud probability score and confidence score, all stored as named fields in AgentState.

