# Annotation Guide – `but_manual_review_annotated.csv`

## 1. Purpose and scope

This guide documents the annotation scheme used for the 30-sentence qualitative review sample from the SST-2 validation split. The sample contains:

- all 15 validation examples containing the standalone word `but` that the model classified incorrectly; and
- 15 randomly selected examples containing `but` that the model classified correctly.

The purpose of the annotations is to describe how `but` functions in each sentence and, where meaningful, the sentiment polarity of the material before and after it. These annotations are separate from the original SST-2 labels and from the model's predictions.

All 30 rows were reviewed by one human reviewer. The annotation fields therefore record AI-assisted labels that were checked and reviewed by a human. They do **not** represent independent agreement between two annotators; no inter-annotator agreement score is reported.

## 2. Fields to annotate

| Column | What it records |
|---|---|
| `but_function` | The linguistic function of `but` in the sentence. Use a concise descriptive label that fits the actual context. |
| `first_clause_polarity` | Sentiment polarity of the material before `but`, when a distinct first clause is present and can be interpreted. |
| `second_clause_polarity` | Sentiment polarity of the material after `but`, when a distinct second clause is present and can be interpreted. |
| `annotation_notes` | A short explanation of the decision, including ambiguity or missing context where relevant. |
| `annotation_source` | `AI-assisted annotation; reviewed by human` indicates that AI suggestions were checked by the human reviewer. |
| `annotation_status` | `Human reviewed` indicates that the row was reviewed by the human reviewer. |

Do not change the source or model-output fields, including `idx`, `text`, `label`, `true_label`, `prediction`, `confidence`, or `correct`. In particular, keep the original dataset label `true_label` unchanged.

## 3. Codebook for `but_function`

The labels in the CSV are descriptive, fine-grained labels. They can be understood using the following broader functional categories:

### Contrast

`but` connects two ideas that are opposed, different, or evaluated in contrasting ways. The contrast may be between two clauses, two audiences, an intention and its execution, or two aspects of an evaluation.

Examples of descriptive labels used in the CSV include `Contrast`, `Contrast between audiences`, `Metaphorical contrast`, `Contrast / qualification`, and `Contrast / positive counterpoint`.

### Concession or qualification

The second part limits, qualifies, or counters an expectation created by the first part. It may acknowledge one point while introducing a limitation or counterpoint.

Examples include `Concession / contrast`, `Concession / qualification`, `Concession / boundary-setting`, and `Concession / positive counterpoint`.

### Fixed expression or non-adversative use

`But` is part of a fixed phrase or expression and does not connect two contrasting clauses. Examples include:

- `nothing but` – approximately “merely” or “nothing other than”;
- `anything but` – meaning “not at all” or “far from”; and
- `can't help but` – meaning “cannot avoid doing something”.

Use a descriptive label such as `Fixed expression: “nothing but” (not an adversative connector)` or `Fixed expression: “anything but” (not an adversative connector)`.

### Corrective contrast

A construction such as `but rather` rejects or corrects the first formulation and replaces it with a more accurate one. The CSV uses the descriptive label `Corrective contrast: “but rather”`.

### Discourse continuation with missing antecedent

A sentence may begin with `But`, while the preceding sentence or clause is not included in the review item. In that case, the relationship to the previous discourse cannot be fully established from the available text. Use a label such as `Discourse continuation (antecedent missing)` and explain the missing context in `annotation_notes`.

### Adjective-level or rhetorical contrast

Sometimes `but` contrasts descriptive or rhetorical elements without clearly reversing the overall sentiment. Labels such as `Adjective-level contrast`, `Adjective-level contrast / qualification`, and `Rhetorical contrast / shift of focus` describe these cases.

**Important:** The presence of the token `but` does not by itself prove that a sentence expresses adversative contrast. Classify its function from the sentence's meaning and construction, not from the keyword alone.

## 4. Polarity labels

Use the following values for clause-level polarity:

- `POSITIVE`: clearly favorable or approving language.
- `NEGATIVE`: clearly unfavorable, critical, or disapproving language.
- `NEUTRAL`: primarily descriptive or factual language without a clear positive or negative evaluation.
- `MIXED`: the same clause or segment contains meaningful positive and negative evaluations.
- `UNCLEAR`: the available wording or context is insufficient to make a reliable judgment.
- `N/A`: a clause-level polarity does not apply, for example when `but` is part of a fixed expression and does not divide the sentence into two clauses.

Assess the sentiment expressed by each segment, not whether the sentence agrees with the SST-2 `true_label`. A sentence may contain a neutral first segment and a positive second segment while its overall meaning remains positive.

When a sentence begins with `But` and the preceding clause is absent, there is no first clause to annotate from the supplied text. Leave the first-clause field empty or mark it `N/A`, and choose `UNCLEAR` for the second segment only when its polarity genuinely cannot be determined without the missing context.

For fixed expressions such as `can't help but`, `nothing but`, and `anything but`, do not invent two clause polarities. Use `N/A` for clause-level polarity fields (or leave the cells empty where the spreadsheet represents not-applicable values as blank) and explain the construction in the notes. The overall sentence sentiment, if discussed, belongs in `annotation_notes`, not in the two clause-polarity fields.

## 5. Ambiguity and context rules

1. Prefer the narrowest interpretation supported by the text. Do not assume that an unusual or unrelated description is negative; in reviews, it may praise an unconventional or surreal style.
2. Do not infer a missing first clause when an item begins with `But`.
3. Distinguish the sentiment of a clause from the linguistic function of `but`. A contrast can connect two positive descriptions, or a neutral description and a positive one.
4. Use `MIXED` only when both positive and negative evaluations are genuinely present in the same segment; do not use it merely because the sentence is unusual or difficult.
5. Record the reason for uncertain decisions in `annotation_notes`.

## 6. Examples from the reviewed sample

| ID | Relevant construction | `but_function` | First polarity | Second polarity | Rationale |
|---:|---|---|---|---|---|
| 440 | `...wild surreal stuff, but brilliant...` | `Contrast` | `NEUTRAL` | `POSITIVE` | The reviewer interprets “brilliant” as praise. The unrelated rooms can describe the film's surreal, unconventional style rather than criticize it. |
| 446 | `...nothing but a sticky-sweet soap` | Fixed expression: `nothing but` | Not applicable | Not applicable | `Nothing but` means “merely”; it does not introduce a second contrasting clause. |
| 553 | `...can't help but warmly extend your arms...` | Fixed expression: `can't help but` | Not applicable | Not applicable | The phrase means “cannot avoid doing something”; it is not an adversative connector. The overall sentence is positive. |
| 741 | `But it still jingles in the pocket.` | Discourse continuation (antecedent missing) | Not available | `UNCLEAR` | The preceding discourse is absent, so the contrast cannot be fully interpreted. |
| 321 | `But it could have been worse.` | Discourse continuation (antecedent missing) | Not available | `NEUTRAL` | The sentence conveys mitigation, but the preceding context is missing. `NEUTRAL` is a cautious clause-level label. |

## 7. Sampling and interpretation limitations

The 30-row sample was deliberately balanced by model correctness (15 incorrect and 15 randomly selected correct examples). It is useful for qualitative inspection, but its category counts or proportions must **not** be interpreted as population-level category frequencies or category-specific error rates.

The original `true_label`, model `prediction`, and `confidence` values are retained as source/model information. Clause-polarity annotations are human judgments about parts of the sentence; they are not replacement ground-truth sentiment labels.

## 8. Review status and reporting

All 30 rows have the status `Human reviewed`, meaning that one human reviewer checked the AI-assisted suggestions. This does not imply that a second independent annotator reviewed the rows. Do not claim inter-annotator agreement or independent double annotation unless that work is completed separately.

The annotation review is a qualitative component of Phase I. The quantitative error-rate analyses on all 872 validation examples are based on the original dataset labels and model predictions; changing a qualitative annotation does not alter those quantitative results unless the team explicitly reruns an analysis using the reviewed semantic annotations.
