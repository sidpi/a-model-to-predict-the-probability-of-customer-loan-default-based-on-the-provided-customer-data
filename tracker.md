# Experiment Tracker — Home Credit Default Risk (GCI Global 2026)

## Version history

- **v0.1 (estimate, reconstructed):** Ran official `tutorial.ipynb` baseline end-to-end. *(Estimate — not measured in this session.)*
- **v0.2 (estimate, reconstructed):** Local path handling (`Path.cwd()/"input"`), submission-format check vs `sample_submission.csv`. *(Estimate — not measured in this session.)*
- **v1.0 (estimate, reconstructed):** First `add_features` ratio/EXT/bureau/family/missing engineering + single-model 5-fold CV. *(Estimate — not measured in this session.)*
- **v1.1–v1.3 (estimates, reconstructed):** Iterated EXT interactions (`_SQ/_CUBE`/pairwise PROD/DIFF/RATIO), in-fold frequency + smoothed target encoding, median-imputation-with-indicators; tried LightGBM/XGBoost alongside HGB. *(Estimates — not measured in this session.)*
- **v1.4 (provided context, not re-measured here):** v1.4 features + median impute + single HGB-A, 5-fold. OOF AUC = **0.756505** (folds 0.754928/0.759115/0.760423/0.752399/0.755848) — figure supplied with the v2.0 task; 3-fold ablations and LR OOF 0.744 likewise supplied, not re-run. Artifacts: `tutorial_improved_v1_4.ipynb`, `submission_improved_v1_4.csv` (both left unmodified).
- **v2.0.1 (MEASURED this session — rebuild + CSV-writer fix, executed notebook `tutorial_improved_v2_0.ipynb`, 12 cells):**
  - HGB-A OOF AUC = **0.756665** (folds 0.755131 / 0.756446 / 0.761442 / 0.753367 / 0.757281)
  - HGB-B OOF AUC = **0.754156** (folds 0.752639 / 0.754317 / 0.758925 / 0.750217 / 0.755231)
  - A+B blend OOF AUC = **0.756815** at weights **{hgb_a: 0.8, hgb_b: 0.2}** (grid step 0.05, OOF-selected; exact float 0.19999999999999996 for hgb_b)
  - Rebuild reason: `tutorial_improved_v2_0.ipynb` / `.zip` were deleted; recreated per spec with THE FIX. Scoring-platform `ParserError: Expected 1 fields in line 4, saw 2` indicates the wrong file (likely the `.ipynb` JSON) was uploaded — writer hardened anyway: `to_csv(index=False, lineterminator="\n", encoding="utf-8")` + pandas re-read asserts (columns, 61500 rows, IDs, no NaN, probs in [0,1], no `\r`, header check) + head/describe prints.
  - No other scores claimed; v2.0 numbers reproduced exactly.
- **v2.5 (MEASURED this session — executed notebook `tutorial_improved_v2_5.ipynb`, 13 cells: 1 markdown + 12 code, all with outputs, zero errors):**
  - Ablation (in-notebook, 3-fold, HGB-A seed 42, shared splits): nogb folds [0.755186/0.754857/0.755861] OOF **0.755258**; withgb folds [0.755000/0.754309/0.754454] OOF **0.754545**; DELTA **−0.000713** (groupby negative at 3-fold).
  - Same-session standalone 3-fold probes (HGB-A, seed 42): v2.0 feats OOF 0.755522 (folds 0.755998/0.755492/0.755154); +NEW9 0.755258 (−0.000264, flat); +groupby means/stds/size 0.754696 (−0.000826); class_weight="balanced" 0.753695 (folds 0.754774/0.752718/0.753694 — worse on every fold → DROPPED). HGB-C 3-fold: nogb 0.754504 vs withgb 0.754413 (delta −0.000091, flat).
  - Full run `StratifiedKFold(5, shuffle, seed 42)`, each config seed-averaged over {42,43,44} per fold (12.5 min; kept 5-fold per contract):
    - A0 (HGB-A, NO groupby anchor) OOF **0.757700** (folds 0.755225/0.758879/0.762458/0.753956/0.758162)
    - A1 (HGB-A, WITH groupby) OOF **0.757815** (folds 0.754920/0.759159/0.763042/0.753621/0.758469)
    - C1 (HGB-C lr=0.03/max_iter=1000/leaves=63/min_leaf=50/l2=2.0, WITH groupby) OOF **0.757165** (folds 0.755002/0.757849/0.762825/0.753408/0.757017)
  - Winning blend (OOF-selected: pairs step 0.05 + triples step 0.1): **{a0: 0.3, a1: 0.6, c1: 0.1} OOF 0.757922**.
  - Delta vs v2.0 measured blend 0.756815: **+0.001107**. Target ≥0.759 NOT met — reported honestly (no inflation; 0.759+ remains future work).
  - CSV: `output/submission_improved_v2_5.csv`, 61500 rows, TARGET range [0.005712, 0.692509], Unix newlines, IDs match test.csv + sample_submission.csv.
  - Artifacts: `tutorial_improved_v2_5.ipynb` (executed, figures embedded), `output/submission_improved_v2_5.csv`, `tutorial_improved_v2_5.zip` (ONLY the .ipynb — verified via zipfile listing).
- **v2.6 (MEASURED this session — executed notebook `tutorial_improved_v2_6.ipynb`, 9 code cells, zero markdown/raw, zero errors):**
  - In-notebook ablation (3-fold, HGB-A seed 42, shared splits; exact reproduction of v2.5 ablation baselines): base folds [0.755186/0.754857/0.755861] OOF **0.755258**; rank folds [0.755186/0.754857/0.755861] OOF **0.755258** (DELTA **0.000000** — trees invariant to monotonic rank transform, DROPPED); prunegb folds [0.755966/0.754575/0.754329] OOF **0.754913** (DELTA **−0.000345**, DROPPED); pair2 folds [0.753815/0.753803/0.756155] OOF **0.754547** (DELTA **−0.000711**, DROPPED); LGBM folds [0.758996/0.759601/0.756960] OOF **0.758503** (DELTA **+0.003245**, wins every fold, KEPT).
  - Same-session standalone probes (shared 3-fold splits): full-groupby repro 0.754545 (Δ −0.000713, matches v2.5, DROPPED); pair3 0.754443 / prunegb+pair2 0.754499 / combo 0.754499 (all DROPPED); LGBM-1500/lr0.02/leaf31 imputed 0.757494 vs native-NaN 0.757182 (kept native: 4× faster, 12s vs 47s/fit); LGBM-1000 native **0.758503** (KEPT — better and faster, 9s/fit); prunedGB+pairTE under LGBM 0.755075 (Δ −0.0021, confirms DROP); LGBM-B (800/lr0.03/leaf63) 0.754177 (DROPPED, weaker).
  - Full run `StratifiedKFold(5, shuffle, seed 42)`, each config seed-averaged over {42,43,44,45,46} per fold (12.3 min):
    - A (HGB-A lr=0.035/700/31/80/2.0, base features + freq/smoothed-TE(60), median-impute) OOF **0.757970** (folds 0.755303/0.758892/0.762751/0.754945/0.758168)
    - C (HGB-C lr=0.03/1000/63/50/2.0, same features) OOF **0.757641** (folds 0.755479/0.758130/0.762621/0.754306/0.757957)
    - L (LGBM 1000/lr0.02/31/80/feat0.8/bag0.8/l1 0.1/l2 1.0/deterministic, native NaN, same freq/TE features) OOF **0.761453** (folds 0.757564/0.763843/0.766511/0.757017/0.762534 — wins every fold vs both HGBs)
  - Winning blend (OOF-selected: pairs step 0.05 + triples step 0.1): **{a: 0.0, c: 0.0, l: 1.0} OOF 0.761453** (no HGB mix beats pure LGBM on OOF; shipped as measured).
  - Delta vs v2.5 measured blend OOF 0.757922: **+0.003531**. OOF target ≳0.7595 MET (0.761453). Public LB (measured 10-10-2026, user-reported): **0.76598** vs v2.5 LB 0.76221 (**+0.00377**) — exceeds the 0.7647 target.
  - CSV: `output/submission_improved_v2_6.csv`, 61500 rows, TARGET range [0.002551, 0.757258], Unix newlines, IDs match test.csv + sample_submission.csv.
  - Artifacts: `tutorial_improved_v2_6.ipynb` (executed, 9 code cells, outputs embedded), `output/submission_improved_v2_6.csv`, `tutorial_improved_v2_6.zip` (ONLY the .ipynb — verified via zipfile listing).
- **v3.0 (MEASURED this session — executed notebook `tutorial_improved_v3_0.ipynb`, 9 code cells, zero markdown/raw, zero errors):**
  - In-notebook ablation (3-fold, single seed 42, shared splits): base_lgbm folds [0.758996/0.759601/0.756960] OOF **0.758503** (exact reproduction of v2.6 LGBM baseline, confirms pipeline integrity); new_lgbm (v2.6 features + 17 NEW v3.0 features) folds [0.758877/0.758830/0.757235] OOF **0.758300** (DELTA **−0.000203** → USE_NEW=False, DROPPED); new_lgbm_big (1500/leaf63) OOF **0.753484** (WEAKER, dropped); new_xgb folds [0.760139/0.758698/0.758416] OOF **0.759064** (beats LGBM reference → KEPT); new_hgb OOF 0.754479 (DELTA −0.004024 → HGB dropped from full run).
  - Full run `StratifiedKFold(5, shuffle, seed 42)`, each config seed-averaged over {42,43,44,45,46} per fold (ablation 261 s + full 841 s ≈ 19 min):
    - lgbm (LGBM w/ v2.6-verified params 1000/lr0.02/31/80) OOF **0.761453** (folds 0.757564/0.763843/0.766511/0.757017/0.762534 — identical to v2.6, expected since USE_NEW=False)
    - xgb (XGBoost hist/lossguide 1000/lr0.02/max_leaves31/mcw80/colsample0.8/subsample0.9/α0.1/λ1.0, native NaN) OOF **0.761440** (folds 0.758635/0.762889/0.766420/0.757674/0.761778, avg 172 s/fold with 10 fits/fold)
  - Winning blend (OOF-selected: singles + pairs step 0.05): **{lgbm: 0.5, xgb: 0.5} OOF 0.761980**.
  - Delta vs v2.6 measured blend 0.761453: **+0.000527** (first blend to beat pure LGBM; HGB excluded on measured evidence — lost ablation and its v2.6 blend never won). v2.6-new17 features probed and DROPPED (−0.000203 at 3-fold) — reported honestly above; USE_NEW gated the full run to the validated 103-col feature set.
  - CSV: `output/submission_improved_v3_0.csv`, 61500 rows, TARGET range [0.002606, 0.764328], Unix newlines, IDs match test.csv + sample_submission.csv.
  - Artifacts: `tutorial_improved_v3_0.ipynb` (executed, figures embedded), `output/submission_improved_v3_0.csv`, `tutorial_improved_v3_0.zip` (ONLY the .ipynb — verified via zipfile listing).
- **v3.1 (MEASURED this session — executed notebook `tutorial_improved_v3_1.ipynb`, 9 code cells, zero markdown/raw, zero errors):**
  - In-notebook ablation (3-fold, single seed 42, shared splits, exact v3.0 base reproduction): lgbm_base OOF **0.758503** (folds [0.758996/0.759601/0.756960], identical to v3.0/v2.6, pipeline integrity); lgbm_A (leaf63/mc100/ff0.7) OOF 0.755726 (worse); lgbm_B (n_estimators=2000/lr=0.01) folds [0.759784/0.758674/0.757690] OOF **0.758706** (DELTA +0.000203 → kept as lgbm2); lgbm_C (extra_trees/leaf63) OOF 0.751800 (much worse); xgb_base OOF **0.758948** (best xgb); xgb_A (depthwise/depth7) 0.757555 (worse); xgb_B (600/lr0.03/leaf63) 0.758429 (≤ base, dropped); decisions: USE_XGB=True, extra_xgb=None, extra_lgbm=lgbm2.
  - Full run `StratifiedKFold(5, shuffle, seed 42)`, seed-averaging extended {42–48} (7 seeds); ablation 267 s + full 1835 s (avg ~367 s/fold, 21 fits/fold):
    - lgbm (v2.6-verified params 1000/lr0.02/31/80, 7 seeds) OOF **0.761520** (folds 0.757637/0.763907/0.766572/0.757090/0.762591 — +0.000067 vs 5-seed v3.0)
    - lgbm2 (2000/lr0.01/31/80, 7 seeds) OOF **0.761756** (folds 0.758144/0.764060/0.766509/0.757647/0.762626 — best single model ever measured in this repo)
    - xgb (v3.0 config 1000/lr0.02/leaf31, 7 seeds) OOF **0.761471** (folds 0.758698/0.762892/0.766474/0.757646/0.761862)
  - Winning blend (OOF-selected: singles + pairs step 0.05): **{lgbm2: 0.55, xgb: 0.45} OOF 0.762109** (lgbm weight 0.0).
  - Delta vs v3.0 measured blend 0.761980: **+0.000129**. Delta vs v2.6-pure-LGBM 0.761453: +0.000656. Honest increments this iteration; lgbm_B and 7-seed averaging are the measured winners, lgbm_A/lgbm_C/xgb_A/xgb_B all dropped.
  - CSV: `output/submission_improved_v3_1.csv`, 61500 rows, TARGET range [0.002623, 0.761345], Unix newlines, IDs match test.csv + sample_submission.csv.
  - Artifacts: `tutorial_improved_v3_1.ipynb` (executed, figures embedded), `output/submission_improved_v3_1.csv`, `tutorial_improved_v3_1.zip` (ONLY the .ipynb — verified via zipfile listing).
- **v3.2 (MEASURED this session — executed notebook `tutorial_improved_v3_2.ipynb`, 9 code cells, zero markdown/raw, zero errors):**
  - Full CV reproduced v3.1 exactly (same seeds/features — lgbm 0.761520, lgbm2 0.761756, xgb 0.761471; full 2003 s), confirming determinism across sessions.
  - Blend refinement (finer grids: pairs step 0.02, triples step 0.05): best linear **{lgbm2: 0.56, xgb: 0.44} OOF 0.762109** (same optimum as v3.1's {0.55, 0.45} within grid tolerance).
  - Nested LR-stack probe (honest inner 5-fold seed-111 stacking of the 3 OOF vectors via LogisticRegression, 0.5 s): OOF **0.761969, delta −0.000141 → USE_STACK=False, NOT adopted** (reported honestly; linear weighted blend retained).
  - CSV: `output/submission_improved_v3_2.csv`, 61500 rows, TARGET range [0.002623, 0.761151], Unix newlines, IDs match test.csv + sample_submission.csv.
  - Artifacts: `tutorial_improved_v3_2.ipynb` (executed, figures embedded), `output/submission_improved_v3_2.csv`, `tutorial_improved_v3_2.zip` (ONLY the .ipynb — verified via zipfile listing).
- **v4.0 (MEASURED this session — executed notebook `tutorial_improved_v4_0.ipynb`, 9 code cells, zero markdown/raw, zero errors; ran green ONCE after a config-name fix — first build unconsciously probed `LGBM_BIG`/1500-leaf63 as lgbm3 and shipped OOF 0.761866; builder patched to probe `LGBM_B`/2000-lr0.01 and the notebook was re-executed; shipped numbers are from the corrected executed notebook):**
  - In-notebook ablation (3-fold, single seed 42, shared splits; base reproduction): lgbm_base OOF **0.758503**; lgbm_B (2000/lr0.01) OOF **0.758706** (exact reproduction of v3.1, best LGBM); lgbm_E (leaves15) OOF 0.758636; lgbm_F (1200/lr0.015/leaf63/mc120) OOF 0.756843; **lgbm_dart (500/lr0.05/drop0.15) OOF 0.749903 — far below base, DROPPED**; xgb_base OOF **0.758948** (best XGB); xgb_E (700/lr0.03/mcw120) OOF 0.758588; **xgb_dart (400/lr0.05/rate_drop0.1/one_drop) OOF 0.743228 — far below base, DROPPED**; decisions: best_lgbm=lgbm3(B), USE_XGB=True, dart NOT adopted, adopted models {lgbm3, xgb}.
  - Full run `StratifiedKFold(5, shuffle, seed 42)`, lgbm3 + xgb seed-averaged over {42–48} (ablation 501 s + full 1478 s, dart rejected so no extra fits):
    - lgbm3 (2000/lr0.01/31/80, 7 seeds) OOF **0.761756** (folds identical to v3.1/v3.2 lgbm2)
    - xgb (1000/lr0.02/leaf31, 7 seeds) OOF **0.761471** (identical to v3.1/v3.2)
  - Winning blend (OOF-selected: singles + pairs step 0.02): **{lgbm3: 0.56, xgb: 0.44} OOF 0.762109** (equal to v3.2 optimum; predictions bit-identical to v3.2 CSV).
  - Nested LR-stack probe: OOF 0.762005, delta −0.000105 → NOT adopted (linear retained; stack has now lost in both v3.2 and v4.0).
  - Honest verdict: v4.0 OOF gain = **0.000000** — dart modes and wider HP breadth all measured worse than the v3.1 winners; the ablation gate correctly shipped the unchanged-winning config. OOF remains 0.762109.
  - CatBoost route was attempted first via `pip install catboost` but no Python-3.14 wheel is available in this environment (install timed out / module absent) — excluded as planned-fallback; dart probes replaced it as the new-model-family lever.
  - CSV: `output/submission_improved_v4_0.csv`, 61500 rows, TARGET range [0.002623, 0.761151], Unix newlines, IDs match test.csv + sample_submission.csv, predictions verified identical to `submission_improved_v3_2.csv`.
  - Artifacts: `tutorial_improved_v4_0.ipynb` (executed, figures embedded), `output/submission_improved_v4_0.csv`, `tutorial_improved_v4_0.zip` (ONLY the .ipynb — verified via zipfile listing).

## v2.0 model config

- Features: `add_features_v2` applied identically to train/test — all v1.4 ratio/EXT/bureau/family/missing features plus `LOG_AMT_INCOME_TOTAL/LOG_AMT_CREDIT/LOG_AMT_ANNUITY/LOG_AMT_GOODS_PRICE` (log1p), `ANNUITY_CREDIT_RATE`, `INCOME_PER_CHILD` (income/(children+1)), `OWN_CAR_MISSING` + `OWN_CAR_DECADE`, `ORG_IS_XNA`, `RATING_POP_INTERACT` (=REGION_RATING_CLIENT×REGION_POPULATION_RELATIVE); trailing inf→NaN. Train 171202×94, test 61500×94.
- Encoding: in-fold frequency + smoothed target encoding (smoothing=60) for the 8 categoricals (NAME_CONTRACT_TYPE, CODE_GENDER, NAME_INCOME_TYPE, NAME_EDUCATION_TYPE, NAME_FAMILY_STATUS, NAME_HOUSING_TYPE, OCCUPATION_TYPE, ORGANIZATION_TYPE), fit on train folds only. pandas-3-robust categorical detection (object dtype + str-dtype check) cross-checked against hardcoded CAT8.
- Imputation: `SimpleImputer(strategy="median", add_indicator=True)` fit on train folds only.
- Models: HGB-A (lr=0.035, max_iter=700, max_leaf_nodes=31, min_samples_leaf=80, l2=2.0) and HGB-B (lr=0.06, max_iter=500, max_leaf_nodes=63, min_samples_leaf=40, l2=0.5); test preds fold-averaged then blended with OOF-winning weights, clipped to [0,1]. LR probed in v1.4 context only (OOF 0.744, adds nothing) and excluded from the final ensemble.
- Seeds: 42 everywhere (`numpy`, `random`, `StratifiedKFold`, both HGBs).
- Validation: `StratifiedKFold(n_splits=5, shuffle=True, random_state=42)`; metric ROC-AUC; OOF + per-fold prints in notebook.
- EDA (analysis only, never prediction rules): missing-value bar, TARGET imbalance (8.07% positive), EXT_SOURCE split distributions, AGE_YEARS, log-scale AMTs, default-rate bars (incl. ORGANIZATION_TYPE top-10), numeric correlation heatmap, DAYS_EMPLOYED==365243 anomaly check (18.05% → NaN + flag feature).

## v2.0 pre-submission rule checklist (MUST_FOLLOW_RULES.md §10)

- [x] Only `input/train.csv` and `input/test.csv` used — no external data.
- [x] No test predictions hand-labeled or manually overridden (all from trained HGB-A/HGB-B; EDA anomaly only became a NaN+flag feature).
- [x] Random seeds set (42) wherever applicable.
- [x] Process regenerates the CSV (executed notebook, headless re-runnable).
- [x] Referenced code cited (see below).
- [x] CSV matches `sample_submission.csv`: columns [SK_ID_CURR, TARGET] in order, 61500 rows, IDs equal test SK_ID_CURR, 0 NaN, probs in [0.005790, 0.698502] ⊂ [0,1].
- [x] SK_ID_CURR alignment verified against both test.csv and sample_submission.csv.
- [x] TARGET = model-generated probabilities (fold-averaged HGB blend).
- [x] Intended file: `output/submission_improved_v2_0.csv`. Protected files (`input/`, `tutorial.ipynb`, `tutorial_improved_v1_4.ipynb`, `submission_improved_v1_4.csv`) unmodified.

## v2.0 code citations

- `tutorial.ipynb` (official competition tutorial): `Path.cwd()/"input"` loading pattern, EDA-first workflow, submission-format validation concept.
- `tutorial_improved_v1_4.ipynb`: `add_features` base engineering, in-fold frequency + smoothed (60) target encoding, median-impute-with-indicators, StratifiedKFold(5) loop, HGB-A hyperparameters, OOF grid-search + fold-averaged blending pattern.
- v2.0-original: LOG_AMT_* / ANNUITY_CREDIT_RATE / INCOME_PER_CHILD / OWN_CAR_* / ORG_IS_XNA / RATING_POP_INTERACT features, diverse HGB-B, A+B blend.
- `tutorial_improved_v2_0.ipynb` (for v2.5): `add_features_v2` used VERBATIM, EDA cells reused, hardened CSV writer + zip-only-notebook pattern reused.

## v2.5 model config

- Features: v2.0 `add_features_v2` (train 171202×94) + NEW9 row features (train 171202×103): `AGE_YEARS_SQ`, `EXT_SOURCE_MEAN_SQ`, `ANNUITY_GOODS_RATIO` (=AMT_ANNUITY/AMT_GOODS_PRICE; CREDIT_TERM_PROXY already existed as CREDIT_ANNUITY_RATIO), `INCOME_PER_ADULT`, `LOG_INCOME_PER_FAMILY`, `EMPLOYED_AGE_INTERACT` (=EMPLOYED_YEARS×AGE_YEARS), `EXT_MISS_PATTERN` (0–7 bitmask), `BUREAU_REQUEST_ANY`, `CAR_AGE_INCOME_INTERACT`; inf→NaN; identical train/test application.
- Groupby: per each of 8 CAT8 categoricals, in-fold means of EXT_SOURCE_MEAN/AGE_YEARS/AMT_CREDIT/AMT_INCOME_TOTAL/ANNUITY_INCOME_RATIO + group size (48 cols), maps fit on train folds only, unseen → train global stat. Stds variant probed at 3-fold (0.754696, no gain, not used). No leakage (train-fold-only fitting alongside freq/TE/imputer).
- Models: A0 = HGB-A anchor without groupby; A1 = HGB-A with groupby; C1 = HGB-C (lr=0.03, max_iter=1000, max_leaf_nodes=63, min_samples_leaf=50, l2=2.0) with groupby; each seed-averaged over {42,43,44} per fold; test preds fold-averaged then blended with OOF-winning weights {a0:0.3,a1:0.6,c1:0.1}, clipped [0,1]. class_weight="balanced" probed (3-fold 0.753695, worse every fold) and excluded.
- Seeds: 42 (numpy/random/SKF/splits) + {42,43,44} model-seed averaging.
- Validation: `StratifiedKFold(n_splits=5, shuffle=True, random_state=42)` throughout; 3-fold ablation used shared splits for the paired nogb/withgb check.

## v2.5 pre-submission rule checklist (MUST_FOLLOW_RULES.md §10)

- [x] Only `input/train.csv` and `input/test.csv` used — no external data.
- [x] No test predictions hand-labeled or manually overridden (all from trained HGB A0/A1/C1 blend; groupby/TE are in-fold pipeline features, never prediction rules).
- [x] Random seeds set (42 for splits; {42,43,44} model-seed averaging).
- [x] Process regenerates the CSV (executed notebook, headless re-runnable; green first try).
- [x] Referenced code cited (notebook markdown + Code citations above).
- [x] CSV matches `sample_submission.csv`: columns [SK_ID_CURR, TARGET] in order, 61500 rows, IDs equal test SK_ID_CURR and sample_submission SK_ID_CURR, 0 NaN, probs in [0.005712, 0.692509] ⊂ [0,1], Unix newlines, header `SK_ID_CURR,TARGET`.
- [x] TARGET = model-generated probabilities (OOF-winning A0/A1/C1 blend, clipped).
- [x] Intended file: `output/submission_improved_v2_5.csv`. Protected files unmodified (verified).

## v2.6 model config

- Features: v2.5 `add_features_v2` + NEW9 row features VERBATIM (train 171202×103, test 61500×103) + in-fold frequency + smoothed (60) target encoding of CAT8 only. Rank/percentile (7 cols, concat train+test rank(pct=True), label-free), pruned groupby (9 cols: EXT_SOURCE_MEAN+AGE means + sizes per OCCUPATION_TYPE/ORGANIZATION_TYPE/NAME_EDUCATION_TYPE), and pair-TE (OCCUPATION_TYPE×ORGANIZATION_TYPE, NAME_EDUCATION_TYPE×OCCUPATION_TYPE) were all probed in-fold and DROPPED after measuring zero/negative deltas. No leakage (all maps fit on train folds only; rank is unsupervised).
- Models: HGB-A and HGB-C on median-imputed matrices (SimpleImputer fit on train folds, add_indicator=True); LGBM on the same freq/TE matrices with native NaN handling (no imputer); each seed-averaged over {42,43,44,45,46} per fold; test preds fold-averaged then OOF-winning weights {a:0.0,c:0.0,l:1.0} (pure LGBM), clipped [0,1].
- Seeds: 42 (numpy/random/SKF/splits) + {42,43,44,45,46} model-seed averaging; LGBM deterministic=True.
- Validation: `StratifiedKFold(n_splits=5, shuffle=True, random_state=42)`; 3-fold shared-split ablations precede the full run.

## v2.6 pre-submission rule checklist (MUST_FOLLOW_RULES.md §10)

- [x] Only `input/train.csv` and `input/test.csv` used — no external data (lightgbm 4.7.0 is a modeling library, not data; rank/groupby/TE stats all computed in-fold or unsupervised from these two files only).
- [x] No test predictions hand-labeled or manually overridden (TARGET = pure LGBM model output; blend weights OOF-selected, shipped as measured even though pure-single-model).
- [x] Random seeds set (42 for splits; {42–46} model-seed averaging; LGBM deterministic).
- [x] Process regenerates the CSV (executed notebook, headless re-runnable; green via `python -m nbconvert --to notebook --execute --ExecutePreprocessor.timeout=1800`, full run 12.3 min).
- [x] Referenced code cited (notebook print cell + Code citations below).
- [x] CSV matches `sample_submission.csv`: columns [SK_ID_CURR, TARGET] in order, 61500 rows, IDs equal test SK_ID_CURR and sample_submission SK_ID_CURR, 0 NaN, probs in [0.002551, 0.757258] ⊂ [0,1], Unix newlines, header `SK_ID_CURR,TARGET`.
- [x] TARGET = model-generated probabilities (OOF-winning selection, clipped).
- [x] Intended file: `output/submission_improved_v2_6.csv`. Protected files unmodified (only read; new files dated 10-10-2026 16:28, protected files untouched).
- [x] HARD FORMAT RULE: notebook has ZERO markdown/raw cells (9 code cells only, verified post-execution) and code cells have NO comment lines and NO docstrings (verified via `ast.parse` + hash/triple-quote scan of every cell source; plot titles/labels are string literals only). Zip created post-execution via script (no stale-zip issue), contains ONLY the .ipynb.

## v2.6 code citations

- `tutorial_improved_v2_5.ipynb`: `add_features_v2` + NEW9 + in-fold freq/smoothed-TE(60) + median-impute + StratifiedKFold(5) + OOF grid-search blend pattern reused VERBATIM; HGB-A/HGB-C hyperparameters reused.
- LightGBM (lightgbm 4.7.0, `LGBMClassifier`): standard gradient-boosting library API (no copied external code); parameters (lr 0.02, num_leaves 31, min_child_samples 80, feature_fraction 0.8, bagging 0.8/1, lambda_l1 0.1, lambda_l2 1.0) selected by the measured 3-fold ablation above.

## v3.0 model config

- Features: v2.6 `add_features_v2` + NEW9 VERBATIM (103 cols, train 171202×103 / test 61500×103). NEW17 v3.0 candidates — `EXT_SOURCE_TRIPLE`, `EXT_SOURCE_{1,2,3}_AGE_PROD`, `EXT_SOURCE_{1,2,3}_AGE_RATIO`, `EXT_SOURCE_{1,2,3}_EMPLOYED_PROD`, `OCC_IS_MISSING`, `INCOME_MINUS_ANNUITY`, `GOODS_INCOME_RATIO`, `BUREAU_YEAR_SHARE`, `PHONE_AGE_RATIO`, `CREDIT_PER_CHILD`, `EXT_MEAN_RATING_PROD` (120 cols) — built and asserted, then measurably DROPPED by the 3-fold ablation (−0.000203) and excluded from the shipped features via the USE_NEW flag (no leakage: flag is OOF-driven).
- Encoding: in-fold frequency + smoothed target encoding (smoothing=60) for CAT8, fit on train folds only.
- Models: lgbm = LGBMClassifier (n_estimators=1000, lr=0.02, num_leaves=31, min_child_samples=80, feature_fraction 0.8, bagging 0.8/1, λl1 0.1, λl2 1.0, deterministic=True), native NaN, no imputer; xgb = XGBClassifier (n_estimators=1000, lr=0.02, max_leaves=31, grow_policy=lossguide, tree_method=hist, min_child_weight=80, colsample_bytree=0.8, subsample=0.9, α0.1, λ1.0, eval_metric=auc), native NaN. HistGradientBoosting probed in ablation (0.754479, worse) and excluded.
- Seeds: 42 (numpy/random/SKF/splits) + {42,43,44,45,46} model-seed averaging; test preds fold-averaged then OOF-selected blend weights {lgbm:0.5, xgb:0.5}, clipped [0,1].
- Validation: `StratifiedKFold(n_splits=5, shuffle=True, random_state=42)`; 3-fold shared-split ablation precedes the full run and gates USE_NEW/USE_XGB/USE_HGB programmatically.
- EDA (analysis only, never prediction rules): v2.6 set (missing bar, TARGET imbalance, DAYS_EMPLOYED 365243 anomaly, AGE dist, EXT_SOURCE_2/3 KDEs by TARGET) plus NEW: default-rate bars by EXT_SOURCE_2 decile, by AGE_YEARS bins, by REGION_RATING_CLIENT, and TARGET-correlation bar of 15 key numeric features.

## v3.0 pre-submission rule checklist (MUST_FOLLOW_RULES.md §10)

- [x] Only `input/train.csv` and `input/test.csv` used — no external data (xgboost 3.4.1 is a modeling library, not data; dropped 17-candidate features were built from these files only).
- [x] No test predictions hand-labeled or manually overridden (TARGET = OOF-selected lgbm/xgb blend output; ablation-gated USE_NEW is measured, programmatic feature selection, not a prediction rule).
- [x] Random seeds set (42 for splits; {42–46} model-seed averaging; LGBM deterministic; XGB random_state per seed).
- [x] Process regenerates the CSV (executed notebook, headless re-runnable; green via `python -m nbconvert --to notebook --execute --ExecutePreprocessor.timeout=2400`).
- [x] Referenced code cited (notebook print cell + Code citations below).
- [x] CSV matches `sample_submission.csv`: columns [SK_ID_CURR, TARGET] in order, 61500 rows, IDs equal test SK_ID_CURR and sample_submission SK_ID_CURR, 0 NaN, probs in [0.002606, 0.764328] ⊂ [0,1], Unix newlines, header `SK_ID_CURR,TARGET` (validated in-notebook AND by standalone script).
- [x] TARGET = model-generated probabilities (OOF-winning blend weights, clipped).
- [x] Intended file: `output/submission_improved_v3_0.csv`. Protected files unmodified (input/, tutorial.ipynb, tutorial_improved_v1_4/, submission_improved_v1_4.csv, v2.6 artifacts untouched — verified by mtime listing).
- [x] HARD FORMAT RULE: notebook has ZERO markdown/raw cells (9 code cells only) and code cells have NO comment lines and NO docstrings (verified via `ast.parse` + `#`/triple-quote scan of every cell source; plot titles/labels are string literals only). Zip created post-execution via script, contains ONLY the .ipynb.

## v3.0 code citations

- `tutorial_improved_v2_6.ipynb`: add_features_v2 + add_row_features_v2_5 + freq/smoothed-TE(60) + encode helpers + StratifiedKFold(5) + OOF grid-search blend + hardened CSV writer reused VERBATIM.
- XGBoost (xgboost 3.4.1, `XGBClassifier`): standard gradient-boosting library API (no copied external code); parameters (hist/lossguide, max_leaves 31, min_child_weight 80) selected by the measured 3-fold ablation above.
- New v3.0 candidate features: EXT-source polynomial/age/employed interactions and ratio features derived from the provided column descriptions and measured by ablation (kept only if they improved OOF — they did not, and were dropped).

## v3.1 model config

- Features: UNCHANGED v2.6-verified 103-col set (v3.0 measured the 17 NEW candidates at −0.000203 and they stayed dropped); in-fold frequency + smoothed target encoding (60) of CAT8, fit on train folds only.
- Models (all native NaN, no imputer, test preds fold-averaged, OOF weights {lgbm2:0.55, xgb:0.45}, clipped [0,1]):
  - lgbm = LGBMClassifier(1000, lr0.02, leaves31, mc80, ff0.8, bag0.8/1, λl1 0.1, λl2 1.0, deterministic) seeds {42–48}
  - lgbm2 = LGBMClassifier(2000, lr0.01, leaves31, mc80, ff0.8, bag0.8/1, λl1 0.1, λl2 1.0, deterministic) seeds {42–48}
  - xgb = XGBClassifier(1000, lr0.02, lossguide/max_leaves31/hist, mcw80, colsample0.8, subsample0.9, α0.1, λ1.0, auc) seeds {42–48}
- Seeds: 42 (numpy/random/SKF/splits) + {42,43,44,45,46,47,48} model-seed averaging.
- Validation: `StratifiedKFold(n_splits=5, shuffle=True, random_state=42)`; 3-fold shared-split ablation gates lgbm2 inclusion (winner params by measured OOF, ties→base) and xgb inclusion (max xgb ≥ max lgbm reference).
- Runtime: total ≈ 35 min (ablation 267 s, full 1835 s).

## v3.1 pre-submission rule checklist (MUST_FOLLOW_RULES.md §10)

- [x] Only `input/train.csv` and `input/test.csv` used — no external data.
- [x] No test predictions hand-labeled or manually overridden (TARGET = OOF-selected blend of three trained GBMs).
- [x] Random seeds set (42 splits; {42–48} model-seed averaging; LGBM deterministic; XGB random_state per seed).
- [x] Process regenerates the CSV (executed notebook, headless re-runnable; green via `python -m nbconvert --to notebook --execute --ExecutePreprocessor.timeout=3000`).
- [x] Referenced code cited (notebook print cell + Code citations below).
- [x] CSV matches `sample_submission.csv`: columns [SK_ID_CURR, TARGET] in order, 61500 rows, IDs equal test SK_ID_CURR and sample_submission SK_ID_CURR, 0 NaN, probs in [0.002623, 0.761345] ⊂ [0,1], Unix newlines, header `SK_ID_CURR,TARGET` (in-notebook AND standalone validation).
- [x] TARGET = model-generated probabilities.
- [x] Intended file: `output/submission_improved_v3_1.csv`. Protected files unmodified (input/, tutorial.ipynb, v1.4 artifacts, v2.6 artifacts, v3.0 artifacts untouched).
- [x] HARD FORMAT RULE: notebook has ZERO markdown/raw cells (9 code cells only) and code cells have NO comment lines and NO docstrings (verified via `ast.parse` + scan of every cell source). Zip created post-execution via script, contains ONLY the .ipynb.

## v3.1 code citations

- `tutorial_improved_v3_0.ipynb`: whole pipeline (features, encode_all/encode_pair, ablation gating, full CV, blend grid, hardened CSV writer) reused; v3.1 changes are LGBM/XGB hyperparameter probes, 7-seed averaging, and spec-name de-duplication.
- LightGBM `extra_trees`/`n_estimators=2000`/`feature_fraction` variants and XGBoost `grow_policy=depthwise`/600-iter variants: standard library parameters selected by the measured 3-fold ablation (no external code copied).

## v3.2 model config

- Identical to v3.1 (features 103-col v2.6 set; lgbm/lgbm2/xgb with {42–48}; 5-fold seed 42) — v3.2 only changes the blend cell: finer weight grids and a nested LR-stack probe whose outcome gates whether the final predictions come from the stack (LogisticRegression(max_iter=1000) refit on all OOF, applied to test preds) or the linear weighted blend. Measured outcome: linear.

## v3.2 pre-submission rule checklist (MUST_FOLLOW_RULES.md §10)

- [x] Only `input/train.csv` and `input/test.csv` used — no external data.
- [x] No test predictions hand-labeled or manually overridden (TARGET = OOF-selected linear blend of three trained GBMs; stack probe is a programmatic, OOF-measured decision).
- [x] Random seeds set (42 splits; 111 for nested stack probe; {42–48} model-seed averaging).
- [x] Process regenerates the CSV (executed notebook, headless re-runnable; green via `python -m nbconvert --to notebook --execute --ExecutePreprocessor.timeout=3000`).
- [x] Referenced code cited (notebook print cell + Code citations below).
- [x] CSV matches `sample_submission.csv`: columns [SK_ID_CURR, TARGET] in order, 61500 rows, IDs equal test SK_ID_CURR and sample_submission SK_ID_CURR, 0 NaN, probs in [0.002623, 0.761151] ⊂ [0,1], Unix newlines, header `SK_ID_CURR,TARGET` (in-notebook AND standalone validation).
- [x] TARGET = model-generated probabilities.
- [x] Intended file: `output/submission_improved_v3_2.csv`. Protected files unmodified.
- [x] HARD FORMAT RULE: notebook has ZERO markdown/raw cells (9 code cells only) and code cells have NO comment lines and NO docstrings (verified via `ast.parse` + scan of every cell source). Zip created post-execution via script, contains ONLY the .ipynb.

## v3.2 code citations

- `tutorial_improved_v3_1.ipynb`: entire pipeline reused; logistic-regression stacking probe is standard practice implemented with sklearn `LogisticRegression` + sklearn `StratifiedKFold` (no external code copied).

## v4.0 model config

- Features: UNCHANGED v2.6-verified 103-col set; in-fold frequency + smoothed target encoding (60) of CAT8, fit on train folds only.
- Probes (3-fold, seed 42, shared splits): lgbm {base 1000/lr0.02/31/80, B 2000/lr0.01/31/80, E leaves15, F 1200/lr0.015/leaf63/mc120, dart 500/lr0.05/drop_rate0.15/skip_drop0.5}, xgb {base 1000/lr0.02/leaf31/mcw80, E 700/lr0.03/leaf31/mcw120, dart 400/lr0.05/rate_drop0.1/one_drop}. Gating: best-LGBM by measured OOF shipped as lgbm; up to 2 extra LGBM configs adopted only if ≥ best; dart adopted only if ≥ best non-dart of its family (NOT met); xgb family included if best xgb ≥ best lgbm (met). Adopted: {lgbm3 (2000/lr0.01), xgb (1000/lr0.02)}.
- Models in full run (native NaN, no imputer, test preds fold-averaged, OOF weights {lgbm3:0.56, xgb:0.44}, clipped [0,1]): lgbm3 and xgb seed-averaged over {42–48}.
- Seeds: 42 (numpy/random/SKF/splits) + {42–48} model-seed averaging; dart probes used single seed 42 at 3-fold only.
- Validation: `StratifiedKFold(n_splits=5, shuffle=True, random_state=42)`; nested LR-stack probe (inner 5-fold, seed 111) retained — measured loss again (−0.000105) → linear blend shipped.
- Runtime: ablation 501 s, full 1478 s (~33 min total).

## v4.0 pre-submission rule checklist (MUST_FOLLOW_RULES.md §10)

- [x] Only `input/train.csv` and `input/test.csv` used — no external data (CatBoost was NOT installed/used; dart probes are library features of lightgbm/xgboost).
- [x] No test predictions hand-labeled or manually overridden (TARGET = OOF-selected linear blend of two trained GBMs; dart/HP adoption decided by measured ablation OOF only).
- [x] Random seeds set (42 splits; 111 stack probe; {42–48} model-seed averaging).
- [x] Process regenerates the CSV (executed notebook, headless re-runnable; green via `python -m nbconvert --to notebook --execute --ExecutePreprocessor.timeout=5400`; one honest config-name fix + re-execution documented above).
- [x] Referenced code cited (notebook print cell + Code citations below).
- [x] CSV matches `sample_submission.csv`: columns [SK_ID_CURR, TARGET] in order, 61500 rows, IDs equal test SK_ID_CURR and sample_submission SK_ID_CURR, 0 NaN, probs in [0.002623, 0.761151] ⊂ [0,1], Unix newlines, header `SK_ID_CURR,TARGET` (in-notebook AND standalone validation; also verified bit-identical predictions to v3.2 CSV).
- [x] TARGET = model-generated probabilities.
- [x] Intended file: `output/submission_improved_v4_0.csv`. Protected files unmodified (verified by validate script flow; v2.0–v3.2 artifacts untouched).
- [x] HARD FORMAT RULE: notebook has ZERO markdown/raw cells (9 code cells only) and code cells have NO comment lines and NO docstrings (verified via `ast.parse` + scan of every cell source). Zip created post-execution via script, contains ONLY the .ipynb.

## v4.0 code citations

- `tutorial_improved_v3_2.ipynb`: entire pipeline reused; v4.0 additions are LightGBM `boosting_type="dart"` (drop_rate/skip_drop) and XGBoost `booster="dart"` (rate_drop/one_drop) probes plus two wider LGBM configs and one wider XGB config — all standard library parameters selected/dropped by the measured 3-fold ablation (no external code copied).

## Compliance notes

- Execution used `python -m nbconvert` (nbconvert 7.17.1) instead of `python -m jupyter nbconvert` because the `jupyter-nbconvert` entry point is absent in this environment; identical converter, same `--ExecutePreprocessor.timeout=1200`.
- The in-notebook zip cell ran mid-execution (zipping the pre-execution file), so the zip was re-created after execution from the executed notebook; contents re-verified (12 cells: 1 markdown + 11 code, 11 code cells with outputs, single zip entry).
- v2.0.1 rebuild: notebook recreated locally (no Drive/Colab mount, `Path.cwd()/"input"`), ran green via `python -m nbconvert --to notebook --execute --ExecutePreprocessor.timeout=1200`; one blend-cell `TypeError` (tuple-unpack bug) fixed before green run. CAT8 hardcoded for pandas 3 str-dtype robustness. CSV verified Unix newlines (`\r` absent), header `SK_ID_CURR,TARGET`, 61500 rows.
- Protected files (`input/`, `tutorial.ipynb`, `tutorial_improved_v1_4.ipynb`, `submission_improved_v1_4.csv`) unmodified; `output/submission.csv` (older CRLF file) left untouched.
- v2.5: ran green first try via `python -m nbconvert --to notebook --execute --ExecutePreprocessor.timeout=1800` (full run 12.5 min; kept 5-fold per contract — no 10-fold). In-notebook zip cell ran mid-execution so the zip was re-created after execution from the executed notebook and re-verified (13 cells: 1 markdown + 12 code, 12 code cells with outputs, single zip entry). Protected files re-verified untouched by mtime/listing (`input/`, `tutorial.ipynb`, `tutorial_improved_v1_4.ipynb`, `submission_improved_v1_4.csv`, `tutorial_improved_v2_0.ipynb`/`.zip`, `output/submission_improved_v2_0.csv`); `output/submission_improved_v2_0.csv` left intact. v2.5 CSV independently re-validated outside the notebook (columns/rows/IDs/NaN/range/newlines).
- v2.5.1 (code-only formatting, no logic/score change): stripped `tutorial_improved_v2_5.ipynb` to code cells only (13 → 12 cells: removed 1 markdown, 0 raw; 12 code cells kept exactly with outputs/execution_counts/ids; metadata/kernelspec/language_info unchanged; nbformat valid; all sources `ast.parse` OK). Scores untouched (blend OOF 0.757922, weights {a0:0.3,a1:0.6,c1:0.1}). Re-created `tutorial_improved_v2_5.zip` (ONLY the .ipynb, verified via zipfile listing). Protected files untouched.
- v2.6/v3.x: notebooks are code-only (zero markdown/raw); zips created post-execution via script and verified single-entry; all CSVs independently re-validated outside the notebook (columns/rows/IDs/NaN/range/newlines/header).

## Artifacts

- `tutorial_improved_v2_0.ipynb` (executed, figures embedded)
- `output/submission_improved_v2_0.csv`
- `tutorial_improved_v2_0.zip` (contains ONLY the .ipynb — verified via zipfile listing)
- `tutorial_improved_v2_5.ipynb` (executed, figures embedded)
- `output/submission_improved_v2_5.csv`
- `tutorial_improved_v2_5.zip` (contains ONLY the .ipynb — verified via zipfile listing)
- `tutorial_improved_v2_6.ipynb` (executed, figures embedded)
- `output/submission_improved_v2_6.csv`
- `tutorial_improved_v2_6.zip` (contains ONLY the .ipynb — verified via zipfile listing)
- `tutorial_improved_v3_0.ipynb` (executed, figures embedded)
- `output/submission_improved_v3_0.csv`
- `tutorial_improved_v3_0.zip` (contains ONLY the .ipynb — verified via zipfile listing)
- `tutorial_improved_v3_1.ipynb` (executed, figures embedded)
- `output/submission_improved_v3_1.csv`
- `tutorial_improved_v3_1.zip` (contains ONLY the .ipynb — verified via zipfile listing)
- `tutorial_improved_v3_2.ipynb` (executed, figures embedded)
- `output/submission_improved_v3_2.csv`
- `tutorial_improved_v3_2.zip` (contains ONLY the .ipynb — verified via zipfile listing)
- `tutorial_improved_v4_0.ipynb` (executed, figures embedded)
- `output/submission_improved_v4_0.csv`
- `tutorial_improved_v4_0.zip` (contains ONLY the .ipynb — verified via zipfile listing)
- `README.md` (project summary, updated to v4.0)
- `tracker.md` (this file)
