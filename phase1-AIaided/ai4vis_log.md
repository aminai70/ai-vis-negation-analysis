# AI4Vis Process Log – Phase I

## Context

- **Phase:** Vis4AI Phase I – “How does the model behave?”
- **AI role:** This pair was assigned to use Generative AI support in Phase I.
- **Model under investigation:** `distilbert/distilbert-base-uncased-finetuned-sst-2-english`
- **Task:** English binary sentiment classification on SST-2.
- **Purpose of this log:** Record how ChatGPT contributed to exploration, coding, visualization design, interpretation, and qualitative annotation. This is a concise reconstruction from the project conversation; it is not a verbatim export of every prompt.

## 1. AI-assisted activities

| Activity | Representative user request / prompt (paraphrased) | AI contribution | Human/team action |
|---|---|---|---|
| Environment setup | Help me set up the Phase I project in VS Code with Conda. | Suggested a dedicated `vis4ai` Python 3.11 environment, package checks, and a Jupyter notebook. | User ran each command, shared screenshots, and confirmed the environment and kernel. |
| Model loading | Help load the group's selected DistilBERT sentiment model. | Provided a Transformers text-classification pipeline example and explained labels and scores. | User executed the code and confirmed the model loaded. |
| Initial probing | Help test a few examples, including negation and a possible sarcastic sentence. | Proposed small exploratory inputs and explained that a model score is not accuracy. | User ran predictions; the sarcastic-looking example was treated as a candidate error, not as conclusive evidence. |
| Dataset analysis | Help load and inspect the SST-2 validation split. | Proposed Pandas preprocessing, class counts, missing/duplicate checks, word counts, and batch inference. | User executed the analysis and shared the results. |
| Error analysis | Help inspect the confusion matrix and high-score incorrect cases. | Explained FP/FN and suggested inspecting full texts and grouping candidate error patterns. | User inspected the examples and used them to formulate a contrast-language hypothesis. |
| Feature exploration | Help compare examples containing contrast markers with those without them. | Proposed lexical marker searches, descriptive error-rate comparisons, and Fisher exact tests. | User ran the analyses. The team should describe this as marker presence, not validated semantic contrast. |
| Manual annotation | Please annotate the complete 30-row BUT review file. | Proposed initial BUT-function and clause-polarity labels with short rationales, then grouped the detailed function labels into broader categories. | The user reviewed all 30 rows as a single human reviewer, set their status to `Human reviewed`, and revised individual interpretations including ID 440. No second independent annotator reviewed the cases, and inter-annotator agreement was not calculated. |
| Visualization | Help create interactive plots for error rates, confidence distributions, sentiment strata, and regression estimates. | Suggested Plotly charts and HTML export as HTML files in the project root directory. | User executed the code, reported a notebook rendering issue, installed/checks dependencies, restarted the kernel, and verified that five HTML files were created. |
| Statistical modeling | Help assess whether the BUT association remains after accounting for length and true sentiment. | Suggested Mann–Whitney U and a logistic regression with HC3 robust standard errors, then explained odds ratios and confidence intervals. | User executed the tests and shared outputs. The association for BUT was not statistically significant at the 5% level in the adjusted model. |

## 2. Examples of AI suggestions accepted

- Use a dedicated Conda environment and a notebook for reproducible, stepwise experimentation.
- Use the SST-2 validation split (872 examples) to move beyond a handful of hand-written examples.
- Inspect confusion-matrix counts and individual misclassified texts rather than reporting accuracy alone.
- Compare error rates by lexical-marker presence, sentiment class, and sentence-length group.
- Use a forest plot to display odds ratios and 95% confidence intervals.
- Keep the qualitative annotation file separate from the source review CSV.

## 3. Suggestions refined or corrected

- **Sarcasm interpretation:** An early example was considered a possible sarcasm failure. This was kept as a hypothesis rather than counted as a validated error because sarcasm labels require a reliable ground truth.
- **Contrast-marker grouping:** Initial analysis grouped sentences using words such as `but`, `although`, `though`, `however`, `yet`, `whereas`, `despite`, and `nevertheless`. Inspection showed that marker presence does not guarantee semantic contrast. For example, `nothing but` and `anything but` are fixed expressions, and `but` can mean “except.” The analysis was therefore described as a lexical-marker heuristic.
- **Qualitative taxonomy:** Detailed AI-generated labels were normalized into broader categories (Contrast, Concession, Fixed Expression, Discourse / Other) to make the overview more interpretable. The original detailed labels were retained in the annotated CSV.
- **Human review of ID 440:** The initial MIXED interpretation of the post-BUT material was revised to POSITIVE after human inspection. `Brilliant` is explicit praise, and the unrelated-room description can characterize surreal, unconventional style rather than criticism. The first clause remains NEUTRAL; the BUT function is interpreted as Contrast. The SST-2 label and model output were not changed.
- **Forest plot labels:** The first forest plot displayed predictor labels in the wrong order. The mapping was corrected and the plot regenerated.
- **Notebook execution:** Plotly display initially failed because of an `nbformat` rendering requirement. The package state was checked, the kernel was restarted, and the notebook cells were rerun in order.

## 4. AI suggestions not treated as ground truth

- The AI-generated semantic labels began as suggestions, not ground truth. All 30 cases were subsequently checked by one human reviewer and marked `Human reviewed`. A second independent annotator did not review the cases, and inter-annotator agreement was not calculated.
- Suggested explanations for individual model errors (for example, that a positive word overrode the overall negative meaning) are hypotheses, not proven mechanisms. The black-box Phase I analysis cannot inspect internal model representations.
- The lexical `but` feature is not equivalent to a manually verified contrast label.
- High pipeline scores on incorrect predictions are not, by themselves, evidence of miscalibration. A calibration analysis would require an appropriate evaluation procedure.

## 5. Human review and validation status

1. **Completed:** One human reviewer checked all 30 cases, including the function of `but`, clause polarity where applicable, and ambiguous or non-adversative uses. All 30 rows are marked `Human reviewed` in `but_manual_review_annotated.csv`.
2. **Not performed:** A second independent annotator did not review the cases.
3. **Not calculated:** Inter-annotator agreement was not calculated because there was no second independent annotation set to compare.
4. The current status documents a completed single-reviewer check. It must not be described as independent double annotation or as evidence of inter-annotator reliability.
5. If a second reviewer becomes available, the team can conduct an independent review, document disagreements, adjudicate them, and calculate an appropriate agreement measure.
6. Claims about semantic contrast should be based on the human-reviewed labels and remain limited by the single-reviewer procedure.

## 6. What worked well

- AI helped structure the investigation into small executable steps.
- AI helped translate initial observations into testable comparisons and visual encodings.
- Interactive plots made it possible to inspect distributions and individual examples.
- The iterative screenshot workflow exposed and corrected notebook setup and plot-labeling issues.

## 7. Limitations / lessons learned

- AI can over-interpret a small number of examples and suggest plausible mechanisms without evidence. Hypotheses must be tested across the dataset.
- A lexical marker search is easy to implement but can misclassify the linguistic function of a word.
- Balanced qualitative samples are useful for comparison but cannot estimate population rates.
- AI-generated annotations were checked by one human reviewer across all 30 cases. A second independent annotator did not review them, and inter-annotator agreement was not calculated. The labels remain qualitative judgments, not dataset ground truth.
- Statistical significance should not be inferred from descriptive differences. In the adjusted regression, the BUT term had OR = 1.70, 95% CI = 0.895–3.238, p = 0.1046.
- The log should be updated with the team's actual human annotation decisions, disagreements, accepted/rejected suggestions, and any further prompts.

## 8. Current status

- Five interactive HTML visualizations are saved in the project root directory: `but_function_comparison.html`, `but_error_rate_comparison.html`, `confidence_distribution.html`, `error_rate_by_sentiment.html`, and `logistic_regression_forest_plot.html`.
- Main AI-assisted notebook is `01_load_model_flat.ipynb` in the project root directory.
- The source review data are saved as `but_manual_review.csv`, and the AI-assisted, human-reviewed annotations are saved as `but_manual_review_annotated.csv`, both in the project root directory. The annotation guide is `but_manual_review_annotation_guide.md`.
- All 30 annotation rows have been reviewed by one human reviewer and are marked `Human reviewed`. No second independent annotator reviewed the cases, and inter-annotator agreement was not calculated.
- ID 440 has first-clause polarity NEUTRAL, post-BUT polarity POSITIVE, and BUT function Contrast. ID 553 uses the fixed expression `can't help but`, so both clause-level polarity fields are N/A.
- This log is a conversation-based summary, not a complete verbatim prompt transcript. Add any additional prompts or partner decisions required by the course submission rules.
