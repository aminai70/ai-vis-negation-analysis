# AI Interaction Log – Phase I (AI-assisted pair)

## Scope and record-keeping note

This file summarizes representative interactions with ChatGPT that influenced the AI-assisted analysis. Prompts below are paraphrased for readability; this is not a verbatim transcript. Human execution, checking, and final decisions are noted separately from AI suggestions.

## 1. Environment and model setup

**Representative request:** Help set up the Python/Jupyter environment and load the selected DistilBERT sentiment model.

**AI contribution:** Suggested the use of a Hugging Face `text-classification` pipeline, explained the model's `POSITIVE`/`NEGATIVE` labels and output score, and helped organize the notebook workflow.

**Human action:** The code was run in the project environment; the model was loaded and initial example predictions were inspected. CPU execution was used in the analysis pipeline.

## 2. SST-2 data loading and inference

**Representative request:** Help load the SST-2 validation split, prepare the columns, run inference on all examples, and retain predictions and scores.

**AI contribution:** Suggested a Pandas-based preparation workflow, batch inference, and columns for the true label, prediction, confidence score, and correctness.

**Human action:** The workflow was executed on all 872 validation examples. The resulting DataFrame was used for confusion-matrix and error analysis.

## 3. Error analysis and lexical marker exploration

**Representative request:** Compare model errors in sentences containing `but` with those that do not contain it, and examine other possible contrast markers.

**AI contribution:** Suggested a case-insensitive regular-expression search with word boundaries, group-wise error-rate summaries, confusion/error inspection, and Fisher's exact tests.

**Human action:** The lexical analysis was run and inspected. The team retained the distinction between detecting the word `but` and establishing that it has a semantic contrast function. The observed rates were 13.89% for the 108 `but` examples and 8.25% for the 764 examples without `but`; Fisher's exact test gave p = 0.0696, not significant at the 5% level.

## 4. Sentence length, confidence, and regression

**Representative request:** Explore whether sentence length and the original sentiment label affect the observed relationship between `but` and classification errors.

**AI contribution:** Suggested confidence summaries, a Mann–Whitney U test for sentence length, and a logistic regression with error as the outcome and `but`, scaled word count, and true sentiment as predictors, using HC3 robust standard errors.

**Human action:** The tests and regression were executed and the estimates were interpreted cautiously. The adjusted BUT odds ratio was approximately 1.70 (95% CI 0.895–3.238, p = 0.1046); the BUT term was not statistically significant at the 5% level.

## 5. Interactive visualizations

**Representative request:** Help design interactive plots for error rates, confidence distributions, sentiment strata, and regression estimates.

**AI contribution:** Suggested Plotly visual encodings and HTML export for interactive charts.

**Human action:** The charts were generated, inspected, and saved as HTML files. An initial forest-plot label-order issue was corrected before the final plot was retained.

## 6. Qualitative annotation of 30 BUT examples

**Representative request:** Propose annotations for the function of `but`, clause-level sentiment polarity, and short rationales for 30 selected examples.

**AI contribution:** Generated initial annotations and suggested a broader taxonomy including Contrast, Concession, Fixed Expression, and Discourse / Other.

**Human action:** One human reviewer checked all 30 rows and revised the annotations where needed. The status is `Human reviewed`; the source field identifies the work as AI-assisted and human-reviewed. There was no independent second annotator and no inter-annotator agreement statistic.

### Examples of human review and correction

- **ID 440:** The post-BUT segment was revised from `MIXED` to `POSITIVE`. The final annotation is `Contrast`, first segment `NEUTRAL`, second segment `POSITIVE`. The word “brilliant” is explicit praise; the unrelated-room description can characterize a surreal, unconventional style rather than criticism.
- **ID 553:** `can't help but` was identified as a fixed expression, not an adversative connector. Both clause-polarity fields are `N/A` because the expression does not divide the sentence into two contrasting clauses.
- **General refinement:** The presence of `but` is not sufficient evidence of semantic contrast. Fixed expressions such as `nothing but`, `anything but`, and `can't help but` must be interpreted from their actual construction and context.

## 7. AI suggestions rejected or treated cautiously

- A possible sarcasm explanation for an individual error was treated as a hypothesis, not a verified mechanism.
- AI-proposed explanations for why the model made a particular error were not presented as facts about the model's internal reasoning.
- The lexical `but` feature was not relabeled as a validated semantic-contrast feature.
- A higher error rate in the `but` group was not described as statistically significant because the Fisher test and adjusted regression did not meet the 5% significance threshold.
- Pipeline confidence scores were not described as calibrated probabilities of correctness or as direct sentiment-intensity measures.

## 8. Main lesson

GenAI was useful for structuring the analysis, drafting and debugging code, proposing statistical comparisons, creating visualizations, and suggesting initial labels. Its outputs still required execution checks, careful interpretation, and human validation. The final analysis distinguishes descriptive observations, statistical evidence, and qualitative human judgments.
