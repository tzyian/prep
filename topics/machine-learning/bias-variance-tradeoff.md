# Question Card: Bias–Variance Tradeoff

- Question: Explain the bias–variance tradeoff and how you diagnose and address each.
- Role: ml
- Difficulty: intro

## Short Answer
Bias is error from underfitting; variance is error from overfitting. High bias: poor train and test performance—add capacity/features. High variance: good train but poor test—regularize, get more data, or simplify the model.

## Deep Dive
- Learning curves: train vs validation error.
- Regularization: L1/L2, dropout, early stopping, data augmentation.
- Model capacity and feature engineering.

## Example
```text
Validation error decreases then increases → early stopping point
```

## Follow‑ups
- Cross‑validation and hyperparameter tuning.
- Bias/variance in ensembles.

## Pitfalls
- Data leakage masking overfitting.

## Checklist
- Curves · regularization · capacity · leakage checks

## Sources
- Elements of Statistical Learning (ESL)

