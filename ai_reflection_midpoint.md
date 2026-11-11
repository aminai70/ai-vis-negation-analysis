# AI Reflection – Phase I Midpoint

> **Required identification (complete before submission):**
> - **AI-assisted pair member 1:** [FIRST NAME LAST NAME] – Student ID: [STUDENT ID]
> - **AI-assisted pair member 2:** [FIRST NAME LAST NAME] – Student ID: [STUDENT ID]
>
> Replace the placeholders above with the first and last names and student IDs of both members of the AI-assisted pair. The course instructions require these identifiers at the beginning of this report.

## 1. Project context and goal

In Phase I, our task was to investigate the black-box behaviour of a sentiment-classification model. We used `distilbert/distilbert-base-uncased-finetuned-sst-2-english` on the SST-2 validation split (872 English sentences). The model predicts `POSITIVE` or `NEGATIVE`.

Our AI-assisted investigation focused on model errors and, in particular, whether the error rate differed for sentences containing the standalone word `but` compared with sentences without it. We also examined confidence scores, sentence length, ground-truth sentiment, and qualitative examples.

## 2. How we used Generative AI

We used ChatGPT as an assistant during several stages:

- **Planning and coding:** to break the analysis into manageable notebook steps and suggest code patterns for loading the dataset, running batch inference, constructing a Pandas DataFrame, and extracting incorrect predictions.
- **Exploratory analysis:** to suggest comparisons between sentences with and without `but`, class-stratified summaries, confidence-score summaries, and statistical tests.
- **Statistical interpretation:** to help interpret Fisher's exact test, odds ratios, confidence intervals, and a logistic-regression model with HC3 robust standard errors.
- **Visualization:** to suggest interactive Plotly charts and help correct a forest-plot predictor-label mapping.
- **Qualitative annotation:** to propose initial labels for the function of `but` and the polarity of sentence parts in a 30-example review set.
- **Documentation:** to help organize findings, annotation guidance, and a process log.

The interaction record in `ai-interactions/interaction_log.md` summarizes representative interactions. It is a reconstructed summary, not a verbatim transcript of every prompt.

## 3. What we accepted and what we changed

We accepted suggestions that helped make the analysis reproducible and inspectable, including batch inference, explicit comparison of predictions with ground-truth labels, descriptive error-rate comparisons, and interactive visualizations.

We did not treat AI-generated linguistic interpretations as ground truth. We reviewed the suggested annotations and changed labels or explanations where the sentence context supported a different interpretation.

Examples:

- **ID 440:** The initial interpretation treated the second part as mixed. After human review, we judged the second part positive: the description of unrelated rooms can characterize a surreal or unconventional artistic style rather than express criticism. The reviewed labels are `Contrast`, `NEUTRAL`, and `POSITIVE` for the BUT function, first-clause polarity, and second-clause polarity, respectively.
- **ID 553:** We identified `can't help but` as a fixed expression, not an adversative connector between two clauses. The reviewed function is `Fixed Expression`, and both clause-polarity fields are `N/A`.
- **Other lexical cases:** Expressions such as `nothing but` and `anything but` showed that a simple search for the word `but` cannot reliably identify semantic contrast.

## 4. What the analysis found

On the 872-example validation split, the model achieved 91.06% accuracy (794 correct and 78 incorrect predictions).

The 108 sentences containing the standalone word `but` had 15 errors (13.89%). The 764 sentences without `but` had 63 errors (8.25%). Fisher's exact test gave OR = 1.795 and p = 0.0696. The difference was not statistically significant at the 5% level.

In the adjusted logistic regression, the estimated odds ratio for the `but` term was approximately 1.70, with a 95% confidence interval of approximately 0.895–3.238 and p = 0.1046. This result was also not statistically significant at the 5% level.

These are exploratory associations. They do not show that `but` causes errors. The 30-case annotation sample was deliberately balanced (15 incorrect and 15 correct examples), so its category proportions cannot estimate error rates in the full dataset.

## 5. Strengths and limitations of AI assistance

AI assistance helped us move faster from an initial question to executable analyses, visual summaries, and candidate qualitative labels. It was useful for explaining unfamiliar statistical outputs and for iterating on code and documentation.

However, AI sometimes suggested plausible interpretations that were not sufficiently supported by the sentence context. Linguistic labels required human judgement, especially for mixed evaluations, fixed expressions, and sentences beginning with `but` where preceding context was unavailable. AI-generated code and statistical interpretations also required checking against actual notebook outputs.

The lexical feature `but` is only a word-search heuristic. It does not distinguish adversative contrast from fixed expressions or other uses. The analysis is limited to one model and one validation split, and several exploratory tests were conducted without a multiple-testing correction.

## 6. What we learned and next steps

We learned that AI is useful as a coding, exploration, and annotation assistant, but its suggestions must be checked. A higher model score does not guarantee a correct prediction, and a keyword alone does not capture the full linguistic function of a sentence.

Next, we should preserve the reviewed annotation decisions, ensure all repository documentation agrees with the final CSV, verify the official metadata-file formats, and clearly report the exploratory nature and limitations of the findings.
