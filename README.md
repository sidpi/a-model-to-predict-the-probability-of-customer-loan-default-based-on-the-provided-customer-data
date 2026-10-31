# Home Credit Default Risk — GCI Global 2026

Predict the probability of loan default (`TARGET`) for Home Credit applicants.  
Metric: **ROC-AUC**. Only the competition-provided `input/train.csv` and `input/test.csv` are used; no external data.

## Best result

| Version | OOF AUC (5-fold) | Notes |
|---|---:|---|
| v1.4 | 0.756505 | HGB-A baseline |
| v2.0 | 0.756815 | HGB-A + HGB-B blend |
| v2.5 | 0.757922 | A0/A1/C1 blend |
| v2.6 | 0.761453 | Pure LightGBM |
| v3.0 | 0.761980 | LGBM + XGB blend |
| v3.1 | 0.762109 | + 7-seed averaging |
| v3.2 | 0.762109 | Finer blend grid; LR-stack probe rejected |
| **v4.0** | **0.762109** | Dart-mode probes (LGBM/XGB) + wider HP breadth — all rejected by ablation; gate kept lgbm2+XGB |

Latest submission: `output/submission_improved_v4_0.csv` (predictions identical to v3.2 — measured, shipped as generated)  
Reproducible notebook: `tutorial_improved_v4_0.ipynb` (9 code cells, no markdown/raw cells)

## Method (v4.0)

- **Features:** 103-column v2.6-verified set (ratios, EXT_SOURCE aggregates/interactions, bureau requests, family/missing indicators, log amounts). In-fold frequency + smoothed target encoding (`smoothing=60`) for 8 categoricals. 17 additional v3.0 feature candidates were measured (−0.000203 at 3-fold) and remain dropped.
- **Models:** LightGBM (`2000` trees, lr `0.01`) and XGBoost (hist/lossguide, `1000` trees, lr `0.02`), each seed-averaged over 7 seeds.
- **v4.0 probes (all rejected on measured 3-fold OOF):** LightGBM `dart` (0.749903 vs 0.758503 base), XGBoost `dart` (0.743228) — both far below base; extra LGBM configs (`num_leaves=15`, leaves-63/lr-0.015, extra-trees/deep variants from earlier probes) and extra XGB configs also lost. Candidate-feature and model adoption is always ablation-gated, never hand-picked.
- **Validation:** `StratifiedKFold(n_splits=5, shuffle=True, random_state=42)`; OOF AUC selection.
- **Blend:** OOF-selected linear weights `{lgbm2: 0.56, xgb: 0.44}` = OOF `0.762109`. A nested logistic-regression stack probe has scored below the linear blend in both v3.2 (−0.000141) and v4.0 (−0.000105) and was not adopted.
- Native NaN handling (no imputer) for the gradient-boosting models.

## How to run

```bash
# 1. Place competition data in input/
# 2. Execute the notebook
python -m nbconvert --to notebook --execute --ExecutePreprocessor.timeout=5400 tutorial_improved_v4_0.ipynb
```

The notebook regenerates `output/submission_improved_v4_0.csv` and validates schema/IDs/NaN/range/newlines in-cell.

## Submission format

- Columns: `SK_ID_CURR,TARGET` (exact order)
- Rows: `61500`
- `TARGET` = model-predicted default probability, clipped to `[0, 1]`
- Unix newlines, no missing values

## Compliance

- Only `input/train.csv` and `input/test.csv` used; no external data
- No hand-labeled or manually overridden predictions
- Seeds set throughout (`42` for splits, `{42..48}` for model averaging)
- CSV generated programmatically; validated against `sample_submission.csv`
- Code sources cited in notebook print cells and `tracker.md`

## Requirements

```bash
pip install pandas numpy scikit-learn lightgbm xgboost matplotlib seaborn nbformat
```

Python 3.10+ recommended (tested with pandas 3.0.6, scikit-learn 1.9.1, LightGBM 4.7.0, XGBoost 3.4.1).