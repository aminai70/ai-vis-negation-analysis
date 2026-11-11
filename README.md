# Exploring Negation in Movie-Review Sentiment Classification


## Team Composition

Our group initially had four members. Before work began, one member messaged us saying they had apparently deregistered from the course, so we continued with three members.



Phase I of the AI & Visualization project investigates how a pretrained
sentiment classifier responds to negation edits.

## Research Question

How do negation edits affect positive-class scores and predicted labels?

## Model and Data

The model is `distilbert-base-uncased-finetuned-sst-2-english`.

We use two sets of inputs:

- The SST-2 validation set containing 872 labeled examples.
- A manually curated collection of 40 movie-review excerpt pairs,
  each containing an original text and a negation-based edit.

The review excerpts were collected from Rotten Tomatoes and include
audience and critic reviews. Some inputs are individual sentences
extracted from longer reviews.

## Method

For each pair, we assign an expected direction of score change and
evaluate both versions using the same model.

We calculate the difference between the modified and original
positive-class scores and record whether the predicted label changes.

Edits include adding negation, removing negation, and modifying clauses
within texts containing mixed opinions.

## Visualizations

### Paired-score plot

Blue circles show original scores and orange diamonds show modified
scores. Connecting lines show the size of each change.

The dashed line at 0.5 marks the binary decision boundary. Hovering
reveals the text and score. A dropdown selects a movie or pairs with
changed or unchanged labels.

### Score-change chart

A sorted horizontal bar chart shows the signed score difference for
each pair, making large and small responses easier to compare.

## Results

| Measure | Result |
|---|---|
| SST-2 validation accuracy | 91.06% |
| SST-2 misclassifications | 78 / 872 |
| Curated pairs | 40 |
| Changed predictions | 28 / 40 |
| Unchanged predictions | 12 / 40 |
| Score changes matching expected direction | 40 / 40 |

Directional agreement includes very small changes. For example,
Pair 40 changes by approximately 0.000007 while retaining a negative
prediction.

These observations show that changes in score, changes in predicted
label, and correctness require separate interpretation.

## Running the Project

Use a Python environment with the dependencies listed in
`requirements.txt`.

```bash
python -m pip install -r requirements.txt
python -m jupyterlab
```

Keep `validation-00000-of-00001.parquet` in the same directory as the
notebook.

Open `phase1_negation_analysis.ipynb` and run all cells in order.
An internet connection is required for the initial model download.

The final cell exports the results and interactive charts.

## Files

| File | Purpose |
|---|---|
| `phase1_negation_analysis.ipynb` | Code, analysis, and visualizations |
| `validation-00000-of-00001.parquet` | SST-2 validation data |
| `negation_results.csv` | Results for the 40 pairs |
| `negation_pairs.html` | Interactive paired-score plot |
| `negation_shifts.html` | Interactive score-change chart |
| `requirements.txt` | Python dependencies |

Download the HTML files and open them in a browser to use the
interactive charts. GitHub's file view does not run these charts.

## Limitations

- The 40 pairs form a small, manually selected exploratory collection.
- Expected directions are subjective and edited texts do not have
  independently validated sentiment labels.
- Some excerpts lose context and some pairs originate from the same review.
- Edits differ in scope and some edited sentences are less natural.
- Positive-class scores are not direct measures of sentiment intensity
  or independently calibrated probabilities of correctness.
- Saturated scores can hide changes in the underlying logits.
- Directional agreement is distinct from classification accuracy.
- The experiment describes observable responses without establishing
  their internal cause.

## Tools and Assistance

ChatGPT assisted with writing visualization code.
