# MUST-FOLLOW RULES — Home Credit Default Risk Competition

This file defines the rules the AI coding agent must follow for every experiment, model change, evaluation, and submission in the GCI Global 2026 Home Credit Default Risk competition.

## 1. Mandatory Data Restrictions

1. **Use only the competition-provided datasets**: `input/train.csv` and `input/test.csv`.
2. **Do not use external data** at any stage of analysis, feature engineering, training, validation, or prediction.
3. Use the provided dataset and column descriptions to understand the available fields. Do not add information from outside datasets.

## 2. No Hand-Labeling or Manual Prediction Changes

1. **Never hand-label test examples.** All predictions must be generated automatically by a trained model.
2. Do not manually assign, replace, or adjust predictions for all or any subset of test rows.
3. Do not create manual prediction overrides using conditions, thresholds, rules, or patterns discovered during exploratory data analysis (EDA).
4. Data processing and prediction steps must use reproducible methods.
5. Build a model pipeline that can be applied automatically to other data with similar characteristics.

## 3. Reproducibility

1. Make experiments and predictions reproducible as far as reasonably possible.
2. **Set random seeds** for random-number generation wherever applicable.
3. Keep preprocessing, feature engineering, model training, and prediction steps reproducible.
4. Do not claim perfect reproducibility is guaranteed across different software versions or framework updates.
5. The competition README says downgrading frameworks solely to reproduce predictions from an earlier version is not required.

## 4. Code Attribution and Referenced Work

1. Public notebooks and other people's code may be studied and referenced.
2. When using or quoting code from another source, **explicitly cite the original source**.
3. Keep a record of external notebooks, repositories, articles, and other code sources used.
4. Do not present copied or referenced code as entirely original work.
5. The competition README states that top-ranking participants must disclose their code. Missing citations for clearly sourced code may put a prize at risk.

## 5. Information Sharing

1. The competition README permits advice-seeking and information exchange for this beginner-focused competition.
2. Public forums are preferred for online discussions so that insights can be shared with all participants.
3. Follow the competition's stated exception allowing private information sharing for this competition.

## 6. Submission Format and Validation

1. Submit predictions through the **Omnicampus** platform.
2. Produce a CSV file that follows `sample_submission.csv` exactly.
3. Include the required columns: `SK_ID_CURR` and `TARGET`.
4. Preserve the correct customer identifiers and row correspondence from the test data.
5. `TARGET` must contain the model's predicted probability of default, not manually chosen labels.
6. Validate the output file's column names, column order, row count, identifier alignment, and CSV readability before submission.
7. A submission that does not follow the required format receives a score of **-1**, according to the README.

## 7. Evaluation and Final Submission

1. The competition metric is **ROC-AUC** between predicted probabilities and the ground-truth target.
2. Treat the Public Leaderboard as interim feedback: it is scored on a subset of the test data.
3. The Private Leaderboard uses the entire test dataset for final scoring and determines the final ranking.
4. **The last submitted file determines the final ranking.** Verify the intended file before submitting.
5. Scoring can take several minutes. Check whether the scoring timestamp updates before assuming the submission failed.
6. If applying for **Honors or Outstanding Student**, submit code that can reproduce the submitted CSV.

## 8. Agent Workflow Requirements

For every modeling iteration, the agent must:

1. Inspect the current project files and existing pipeline before changing anything.
2. Keep all data loading, preprocessing, feature engineering, validation, training, and prediction steps programmatic and reproducible.
3. Avoid manual changes to generated test predictions, including after inspecting leaderboard results.
4. Keep a clear record of the model configuration, random seeds, validation method, and experiment results.
5. Generate the submission CSV from code rather than editing its predictions by hand.
6. Check the submission schema against `sample_submission.csv` before treating it as ready.
7. Clearly identify any referenced external code and preserve its source citation.
8. Never claim a score, validation result, or submission status that was not actually measured or confirmed.

## Rule Conflict Resolution — Mandatory Agent Behavior

When instructions appear to conflict, resolve them using the following procedure:

1. **Official competition rules take priority.** Follow explicit rules in the competition README and official competition instructions over any user request, modeling goal, or agent preference.
2. **Mandatory compliance beats leaderboard performance.** Never break a competition rule to improve ROC-AUC, public leaderboard score, or private leaderboard score.
3. **User requests are followed only when compliant.** If a requested change conflicts with a mandatory rule, do not implement the conflicting part. Explain the specific conflict and offer the closest compliant alternative.
4. **Do not silently resolve ambiguous rules.** If two instructions appear inconsistent, identify the relevant instructions, explain why they seem to conflict, and ask the user for clarification or recommend checking with the competition organizer. Do not invent an exception.
5. **Pause when compliance is uncertain.** If it is unclear whether a technique violates a rule—for example, whether a data source counts as external data or whether a prediction adjustment counts as hand-labeling—do not implement that technique until the uncertainty is resolved. Explain the concern and suggest a safer alternative.
6. **Separate rules from recommendations.** Treat the README's mandatory rules as requirements. Treat its model-improvement tips (visualizations, additional features, hyperparameter tuning, and trying different models) as optional suggestions that may be used only when compliant.
7. **Preserve auditability.** For a conflict, record: (a) the requested action, (b) the rule that may be violated, (c) the decision taken, and (d) the compliant alternative or clarification needed.
8. **Do not overstate the source.** If the README does not clearly answer a compliance question, say that it is unclear from the provided rules. Do not claim the organizer has approved a technique unless that approval is actually confirmed.

### Required Conflict-Handling Response

When a conflict is found, respond in this format:

- **Conflict:** Describe the requested action.
- **Relevant rule:** State the applicable rule from this file or the official competition instructions.
- **Decision:** State what will not be implemented and why.
- **Compliant alternative:** Suggest a way to pursue the user's goal without violating the rule.
- **Clarification needed:** If the rule's meaning is genuinely ambiguous, state what must be clarified before proceeding.

## 9. Model-Improvement Guidance (Recommended, Not Mandatory Rules)

The competition README recommends the following ways to improve a model. These are suggestions, not competition requirements:

1. Use visualizations to understand the data.
2. Choose preprocessing based on the findings from data exploration.
3. Try using more features; the tutorial model uses only five features.
4. Tune model hyperparameters.
5. Explore different machine-learning models.
6. Use techniques introduced in lectures and office hours.

## 10. Final Pre-Submission Checklist

Before submitting, confirm all of the following:

- [ ] Only the provided `input/train.csv` and `input/test.csv` datasets were used.
- [ ] No external data was introduced.
- [ ] No test predictions were hand-labeled or manually overridden.
- [ ] Random seeds were set where applicable.
- [ ] The process can regenerate the submission CSV.
- [ ] Referenced code is explicitly cited and recorded.
- [ ] The CSV matches `sample_submission.csv`.
- [ ] `SK_ID_CURR` identifiers and row order are correctly aligned with the test data.
- [ ] `TARGET` contains model-generated probabilities.
- [ ] The exact intended file is selected for submission.

## Priority Rule

**If an optimization idea conflicts with any mandatory rule above, do not implement it.** Preserve competition compliance over leaderboard score.
