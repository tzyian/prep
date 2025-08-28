# Question Card: Precision, Recall, ROC-AUC

- Question: Define precision, recall, and ROC-AUC. When prefer PR AUC over ROC AUC?
- Role: ml
- Difficulty: intro

## Short Answer
Precision is TP/(TP+FP); recall is TP/(TP+FN). ROC AUC measures TPR vs FPR across thresholds; PR AUC focuses on positive class performance. Prefer PR AUC for imbalanced datasets where positives are rare.

## Deep Dive
- Thresholding and PR/ROC curves; F1, Fβ scores.
- Calibration vs discrimination; expected calibration error.
- Imbalance handling: resampling, class weights.

## Example
```text
Spam detection (1% positives): PR AUC more informative than ROC AUC
```

## Follow‑ups
- Precision@K, recall@K; cost‑sensitive metrics.
- Calibration plots and Platt scaling.

## Pitfalls
- Reporting accuracy on imbalanced data.

## Checklist
- Confusion matrix · curves · class imbalance · calibration

## Sources
- Saito & Rehmsmeier (2015) on PR vs ROC

