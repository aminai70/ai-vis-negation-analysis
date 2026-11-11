# AI and Visualization — Phase I

We investigate how DistilBERT behaves on English sentiment classification,
focusing on negation and contrasting statements in movie reviews.

## Model
distilbert/distilbert-base-uncased-finetuned-sst-2-english

We use the pretrained checkpoint without additional training.

## Data
- SST-2 validation split, containing 872 labelled sentences:
  https://huggingface.co/datasets/stanfordnlp/sst2
- Movie-review excerpts and modified sentence pairs for negation experiments.

## Project folders
- [Human-only pair](phase1-human/)
- [AI-aided pair](phase1-AIaided/)

Each folder contains the corresponding code and supporting files.
