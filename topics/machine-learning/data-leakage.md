# Question Card: Data Leakage

- Question: What is data leakage and how do you prevent it during training and evaluation?
- Role: ml
- Difficulty: mid

## Short Answer
Leakage occurs when information from outside the training data’s time or scope leaks into features or labels, inflating metrics. Prevent by correct train/validation splits (time‑aware), building features only from past data, and isolating preprocessing pipelines inside cross‑validation folds.

## Deep Dive
- Types: target leakage, temporal leakage, cross‑entity leakage.
- Pipelines: fit transformers on train fold only; use pipelines to avoid leakage.
- Serving: training/serving skew and online feature leakage.

## Example
```python
Pipeline([('scaler', StandardScaler()), ('clf', LogisticRegression())])
```

## Follow‑ups
- Prevent leakage in feature stores; backfills.
- Detect with adversarial validation.

## Pitfalls
- Fitting scalers/encoders on full dataset before split.

## Checklist
- Split strategy · fold‑safe pipelines · temporal features · monitoring skew

## Sources
- Google ML Practitioners Guide

