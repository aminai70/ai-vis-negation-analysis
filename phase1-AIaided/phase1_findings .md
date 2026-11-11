# Vis4AI – Phase I Findings

## Project
**Model:** `distilbert/distilbert-base-uncased-finetuned-sst-2-english`  
**Task:** Binary sentiment classification (POSITIVE / NEGATIVE)  
**Dataset:** Stanford Sentiment Treebank (SST-2), validation split  
**Analysis notebook:** `01_load_model_flat.ipynb`

> **Status:** Exploratory Phase I analysis. Findings below describe this model on the 872 examples in the SST-2 validation split. Associations are not causal claims. The lexical marker `but` is a heuristic feature, not a verified semantic annotation of contrast.

## 1. Research questions

1. What errors does DistilBERT make on the SST-2 validation split?
2. Is the model's error rate different for sentences containing the lexical item `but`?
3. Do any observed differences remain after accounting for sentence length and ground-truth sentiment?
4. How does the model's output score behave on correct and incorrect predictions?

## 2. Data and method

The validation split contained **872 sentences** and four columns after preprocessing (`idx`, `text`, `label`, `true_label`). The class distribution was:

| Ground-truth class | Count | Share |
|---|---:|---:|
| POSITIVE | 444 | 50.92% |
| NEGATIVE | 428 | 49.08% |

No missing sentence texts or duplicate sentence texts were found in the validation dataframe. Word count was calculated by splitting each text on whitespace. The mean word count was **19.55**, the median was **19**, and the observed range was **2–47** words. This is a simple whitespace-based length measure, not a model-token count.

The selected Hugging Face text-classification pipeline was run on all 872 texts using CPU inference, batches of 16, and truncation enabled. The pipeline's `score` was recorded as the model's score for its predicted class. It should not be interpreted as a guarantee of correctness or as evidence of probability calibration.

## 3. Overall model behaviour

| Measure | Result |
|---|---:|
| Total examples | 872 |
| Correct predictions | 794 |
| Incorrect predictions | 78 |
| Accuracy on this validation split | 91.06% |

### Confusion matrix

Rows are actual labels; columns are predicted labels.

| Actual \ Predicted | NEGATIVE | POSITIVE |
|---|---:|---:|
| NEGATIVE | 381 | 47 |
| POSITIVE | 31 | 413 |

Among actual NEGATIVE examples, 47/428 (10.98%) were predicted POSITIVE. Among actual POSITIVE examples, 31/444 (6.98%) were predicted NEGATIVE. Thus, in this split, false positives were more frequent than false negatives.

## 4. Exploratory analysis of the lexical marker `but`

A case-insensitive regular-expression word-boundary search was used to divide the 872 sentences into those containing the standalone word `but` and those not containing it. This does **not** distinguish adversative `but` from uses such as `nothing but`, `anything but`, or `can't help but`.

| Group | Total | Errors | Error rate |
|---|---:|---:|---:|
| Contains `but` | 108 | 15 | 13.89% |
| Does not contain `but` | 764 | 63 | 8.25% |

The descriptive difference is **5.64 percentage points**. Fisher's exact test gave an odds ratio of **1.795** and a two-sided p-value of **0.0696**. At the 5% significance level, this test does not provide sufficient evidence for a difference. The result is exploratory and should not be stated as a statistically significant effect.

### 4.1 Broader marker heuristic

A second, broader search used the markers `but`, `although`, `though`, `however`, `yet`, `whereas`, `despite`, and `nevertheless`. Sentences matching at least one marker were compared with sentences matching none.

| Group | Total | Errors | Error rate |
|---|---:|---:|---:|
| At least one listed marker | 141 | 18 | 12.77% |
| No listed marker | 731 | 60 | 8.21% |

Fisher's exact test gave an odds ratio of **1.637** and p = **0.1051**. This is not statistically significant at the 5% level. Marker groups may overlap, and the pattern search does not establish that a sentence expresses semantic contrast.

Per-marker counts from the exploratory search were:

| Marker | Matches | Errors | Error rate |
|---|---:|---:|---:|
| `but` | 108 | 15 | 13.89% |
| `despite` | 11 | 0 | 0.00% |
| `though` | 9 | 1 | 11.11% |
| `yet` | 8 | 1 | 12.50% |
| `although` | 6 | 2 | 33.33% |
| `however` | 2 | 0 | 0.00% |
| `whereas` | 0 | 0 | N/A |
| `nevertheless` | 0 | 0 | N/A |

These counts are not mutually exclusive if a sentence contains more than one marker. Very small counts, especially for `although` and `however`, are not a sound basis for ranking marker difficulty.

### 4.2 Error rates by ground-truth sentiment

| Ground-truth class | Contains `but` | Does not contain `but` |
|---|---:|---:|
| NEGATIVE | 10/57 = 17.54% | 37/371 = 9.97% |
| POSITIVE | 5/51 = 9.80% | 26/393 = 6.62% |

Within the NEGATIVE class, Fisher's exact test gave OR = **1.921**, p = **0.1086**. Within the POSITIVE class, OR = **1.534**, p = **0.3821**. Neither comparison is statistically significant at the 5% level. These are exploratory subgroup analyses.

## 5. Confidence-score analysis

The pipeline score for the predicted class was summarized separately for correct and incorrect predictions.

| Group | Prediction status | n | Mean score | Median score |
|---|---|---:|---:|---:|
| Contains `but` | Incorrect | 15 | 0.9329 | 0.9759 |
| Contains `but` | Correct | 93 | 0.9784 | 0.9992 |
| Does not contain `but` | Incorrect | 63 | 0.9473 | 0.9857 |
| Does not contain `but` | Correct | 701 | 0.9884 | 0.9996 |

Some incorrect predictions have high scores. However, the score is not a calibrated correctness probability, and this summary alone is not a calibration evaluation.

### 5.1 High-score subsets

| Score threshold | Group | n | Errors | Error rate |
|---|---|---:|---:|---:|
| ≥ 0.95 | Contains `but` | 94 | 8 | 8.51% |
| ≥ 0.95 | Does not contain `but` | 712 | 43 | 6.04% |
| ≥ 0.99 | Contains `but` | 85 | 7 | 8.24% |
| ≥ 0.99 | Does not contain `but` | 651 | 26 | 3.99% |

Fisher's exact tests gave:

| Threshold | Odds ratio | p-value |
|---|---:|---:|
| ≥ 0.95 | 1.447 | 0.3652 |
| ≥ 0.99 | 2.157 | 0.0903 |

Neither comparison is statistically significant at the 5% level. The ≥0.99 subset is nested inside the ≥0.95 subset, so these tests are not independent replications.

## 6. Sentence length

Sentences containing `but` were longer on average:

| Group | n | Mean words | Median words | SD | Min | Max |
|---|---:|---:|---:|---:|---:|---:|
| Contains `but` | 108 | 24.94 | 25 | 8.75 | 5 | 47 |
| Does not contain `but` | 764 | 18.79 | 18 | 8.50 | 2 | 46 |

The Mann–Whitney U statistic was **57384**. The notebook printed the p-value rounded to six decimal places as `0.0`; this is a rounded display, not a literal probability of zero (the displayed value implies p < 0.0000005 under ordinary rounding).

Error rates by word-count group were:

| Word-count group | Contains `but` | Does not contain `but` |
|---|---:|---:|
| Short (1–10) | 2/5 = 40.00% | 9/147 = 6.12% |
| Medium (11–20) | 3/23 = 13.04% | 29/317 = 9.15% |
| Long (21–30) | 7/51 = 13.73% | 18/224 = 8.04% |
| Very long (31–50) | 3/29 = 10.34% | 7/76 = 9.21% |

The short-`but` group contains only five examples, so its 40% error rate is highly unstable. The other strata also have unequal sample sizes. These descriptive strata do not replace a suitable adjusted statistical model.

## 7. Multivariable logistic regression

A logistic regression model was fitted with error (1 = incorrect, 0 = correct) as the dependent variable and the following predictors: presence of `but`, word count scaled per 10 words, and ground-truth sentiment (`NEGATIVE` as reference). HC3 robust standard errors were requested.

| Predictor | Odds ratio | 95% CI | p-value |
|---|---:|---:|---:|
| Contains `but` | 1.7027 | 0.8953–3.2383 | 0.1046 |
| POSITIVE (vs NEGATIVE) | 0.6117 | 0.3795–0.9858 | 0.0435 |
| Word count (per 10 words) | 1.0593 | 0.8050–1.3938 | 0.6809 |

After adjustment for word count and ground-truth sentiment, the estimated odds of error were higher for sentences containing `but` (OR = 1.70), but the 95% confidence interval includes 1 and p = 0.1046. The data therefore do not provide sufficient evidence for an adjusted association at the 5% level. Word count was not statistically significant in this model. POSITIVE sentiment had a lower estimated odds of error than NEGATIVE sentiment (OR = 0.61, p = 0.0435), but this is an exploratory model among several analyses; it should be interpreted cautiously and considered in light of multiple testing.

The model is observational and includes only a linear word-count term and main effects. It does not establish causation, rule out confounding, test non-linear length effects, or test a BUT-by-sentiment interaction.

## 8. Visualization inventory

The following interactive HTML visualizations were saved in the project root directory:

1. `but_function_comparison.html` – counts of AI-proposed BUT-function categories among a deliberately balanced sample of 15 incorrect and 15 randomly selected correct BUT-containing cases. **This is a case-sample composition chart, not a category error-rate estimate.**
2. `but_error_rate_comparison.html` – descriptive error rates for all 872 validation examples grouped by presence of the token `but`.
3. `confidence_distribution.html` – score distributions split by BUT presence and correctness.
4. `error_rate_by_sentiment.html` – error rates split by ground-truth sentiment and BUT presence.
5. `logistic_regression_forest_plot.html` – adjusted odds ratios and 95% confidence intervals from the logistic regression.

A confusion-matrix table was also produced in the notebook.

## 9. Qualitative error inspection and AI-assisted annotations

A 30-case review set was formed from all 15 incorrect examples containing `but` and 15 randomly selected correct examples containing `but` (random seed 42). ChatGPT proposed initial labels for the function of `but` and the polarity of the clauses. The proposed labels were organized into broad categories (Contrast, Concession, Fixed Expression, and Discourse / Other) and then checked by a human reviewer.

**All 30 rows were reviewed by one human reviewer.** The `annotation_status` field is `Human reviewed` for all 30 cases. The annotations therefore represent AI-assisted suggestions that were checked and reviewed by one person; they are not independently double-annotated labels.

A second independent annotator did not review the 30 cases, and inter-annotator agreement was not calculated. This limitation should be kept in mind when interpreting the qualitative labels. The review is a single-reviewer validation step, not a measurement of agreement between annotators.

For **ID 440**, the human-reviewed interpretation is: first-clause polarity **NEUTRAL**, post-BUT polarity **POSITIVE**, and `but` function **Contrast**. The word `brilliant` is explicit praise, while the description of unrelated rooms can refer to the film's surreal and unconventional style rather than criticize it. This qualitative decision does not change the SST-2 `true_label`, the model prediction, or the confidence score, and it does not alter the aggregate quantitative statistics in this report.

For **ID 553**, `but` occurs in the fixed expression `can't help but`; it does not connect two contrasting clauses. Accordingly, both clause-level polarity fields are marked **N/A**. More generally, expressions such as `nothing but`, `anything but`, and `can't help but` show why a keyword search cannot be equated with adversative contrast.

## 10. Current findings and limitations

### Findings supported by this analysis

- DistilBERT achieved 91.06% accuracy on the 872-example SST-2 validation split used here.
- False positives (47) outnumbered false negatives (31) in this split.
- The observed error rate was higher for the 108 examples containing the standalone word `but` (13.89%) than for the 764 examples without it (8.25%). The unadjusted Fisher test was not significant at 5% (p = 0.0696).
- BUT-containing sentences were longer on average, and the length distributions differed (Mann–Whitney U = 57384; p displayed as 0.000000 after rounding to six decimals).
- In the adjusted logistic regression, the estimated association for `but` remained positive (OR = 1.70) but was not statistically significant (p = 0.1046; 95% CI 0.895–3.238).
- High pipeline scores occurred on some incorrect predictions.

### Limitations

- This is one model and one validation split; results may not generalize to other datasets, domains, or models.
- The `but` feature is a lexical search and includes non-adversative uses. It is not equivalent to a fully validated semantic-contrast feature for the entire dataset.
- The qualitative sample is deliberately balanced by correctness (15 errors and 15 correct cases), so its category proportions cannot estimate population error rates.
- All 30 qualitative cases were reviewed by one human reviewer. A second independent annotator was not available, and inter-annotator agreement was not calculated. The labels therefore reflect single-reviewer judgments and may contain subjective decisions.
- The labels were initially proposed with AI assistance and then checked by the human reviewer; they should not be described as independent double annotation.
- Small subgroup counts produce unstable percentages.
- Multiple exploratory tests were conducted; p-values are not adjusted for multiplicity.
- The regression does not include interactions or flexible non-linear terms and cannot establish causality.
- The pipeline score should not be treated as a calibrated probability of correctness.

## 11. Next steps

1. If resources permit, ask a second independent annotator to review the 30 cases. Compare the two sets of labels, document disagreements, and calculate an appropriate inter-annotator agreement measure before adjudicating differences.
2. Keep the current `Human reviewed` status for all 30 rows as a record of the completed single-reviewer check; do not interpret it as evidence of independent double annotation.
3. Preserve the human-reviewed decisions for ID 440 (first clause NEUTRAL, post-BUT clause POSITIVE, BUT function Contrast) and ID 553 (`can't help but`; both clause-polarity fields N/A) consistently across the annotated data, annotation guide, and process log.
4. Refine the feature to distinguish adversative `but` from fixed expressions and other uses. If feasible, annotate all relevant BUT-containing examples or a pre-defined representative sample.
5. Re-run any semantic-label-based analysis only when the labels and analysis plan are clearly specified. Preserve the notebook, data provenance, random seed, figures, prompts, AI suggestions, and human decisions in the public project repository.
