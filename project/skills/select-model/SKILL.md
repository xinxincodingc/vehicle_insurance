---
name: select-model
description: "A skill to select the best model using three ranked gates (AUC-ROC ≥ 0.78, Recall ≥ 0.70, F1 ≥ 0.15) and document the justification in the shared agent state"
---

## When to use
This skill is triggered after the train-models skill has completed and the evaluation metrics for all trained models are stored in agent state. It selects the best model based on predefined criteria and documents the justification for its selection.

## How to execute
1. Retrieve the evaluation metrics for all trained models from agent state.
2. Screen each model against three ranked gates (in priority order):
   - Gate 1 — AUC-ROC  ≥ 0.78
   - Gate 2 — Recall   ≥ 0.70
   - Gate 3 — F1-score ≥ 0.15
3. Among models that pass all three gates, select the one with the highest AUC-ROC.
4. If no model passes all gates, fall back to highest AUC-ROC overall.
5. Document the selected model and the justification for its selection in the shared agent state.

## Inputs from agent state
- model_metrics: dict — evaluation metrics for all trained models

## Outputs to agent state
- selected_model: Any — the winning model object
- selected_model_name: str — name of the selected model (e.g. "logistic_regression")
- selection_justification: str — plain English justification for model selection

## Post-selection analysis
After the model is selected, the skill produces three additional reports written in plain, non-technical language:

### Baseline comparison
- Compare the selected model against a naive baseline that always predicts "Not Fraud" (baseline recall = 0, AUC-ROC = 0.5, accuracy = proportion of non-fraud claims).
- Show improvement or trade-off on each metric versus this baseline.

### Business interpretation
- Using the confusion matrix, report in plain counts and percentages:
  - How many fraud cases were caught vs. missed.
  - How many legitimate claims were incorrectly flagged vs. correctly cleared.

### Known limitations
- Acknowledge weaknesses such as low precision (high false alarm rate), accuracy trade-offs versus the baseline, and any training warnings (e.g., convergence issues).

## Output format
The output should include the name of the best model, a justification for its selection, a baseline comparison, a business interpretation, and acknowledged limitations — all presented so non-technical stakeholders can understand the operational impact. Selection results are stored as named fields in AgentState.