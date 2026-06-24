# TakeMeter: Hacker News Discourse Classifier

## Overview
TakeMeter is a fine-tuned text classifier for public Hacker News comments. It classifies comments into `evidence_based_reasoning`, `unsupported_claim`, or `conversational_reaction` to measure whether a comment is making a supported argument, asserting an under-supported take, or functioning as a short conversational reply.

## Community Choice
I chose Hacker News because it is public, text-heavy, and centered on technical and social debate. The distinction between a careful explanation and a confident unsupported claim matters to HN participants because thread quality depends on whether comments add evidence, experience, and reasoning instead of just reaction.

## Label Taxonomy
- `evidence_based_reasoning`: The comment makes a claim and supports it with concrete reasoning, technical detail, examples, data, causal explanation, or first-hand experience.
  - Example: "The extra latency is probably from the new auth check. It adds a database lookup on every request, so even a 10ms query becomes visible at p95 under load."
  - Example: "I used this queue pattern at work; it simplified the code, but incident response got harder because state was split across two systems."
- `unsupported_claim`: The comment makes a confident judgment, prediction, or broad claim without enough evidence or reasoning inside the comment to support it.
  - Example: "This startup is obviously doomed and nobody serious will use it."
  - Example: "Rust is just better than Go for everything serious."
- `conversational_reaction`: The comment is mainly a short reply, question, joke, agreement, clarification request, or emotional reaction rather than a developed argument.
  - Example: "Interesting, do you have a link to the benchmark?"
  - Example: "Thanks, that explanation helped."

## Dataset and Labeling Process
The dataset is `takemeter_hn_discourse_dataset.csv`, collected from public Hacker News comments through the Hacker News Algolia API. It contains 238 examples with this label distribution:

- `evidence_based_reasoning`: 80
- `unsupported_claim`: 80
- `conversational_reaction`: 78

The file includes source URLs for traceability. The initial labeling pass used the definitions above plus a rule-assisted/AI-assisted classifier. The CSV `notes` column records why each example received its label.

Three difficult-to-label cases:

1. A short comment asking for a source on a technical claim could look analytical, but I label it `conversational_reaction` because it is a thread-management reply rather than a developed argument.
2. A long comment with many confident claims but no concrete evidence remains `unsupported_claim`; length alone is not enough.
3. A personal anecdote can be `evidence_based_reasoning` if it explains what happened and why it matters, but `unsupported_claim` if it only says something was good or bad.

## Fine-Tuning Approach
I started from `distilbert-base-uncased` and fine-tuned it as a three-label sequence classifier. The notebook uses a 70/15/15 stratified train/validation/test split, 3 epochs, learning rate `2e-5`, batch size `16`, weight decay `0.01`, and `max_length=256`. I kept 3 epochs because the dataset is small and extra epochs would increase overfitting risk.

## Baseline
The baseline is Groq `llama-3.3-70b-versatile` in a zero-shot setting. The prompt defines the three labels, gives one example per label, and instructs the model to output only the exact label name. The notebook reads the Groq key from `.env` as `GROQ_API_KEY`. The baseline and fine-tuned model are evaluated on the same held-out test set.

## Evaluation Report
Run the notebook in Colab with your Groq key to fill in the final numbers. The final notebook cells will generate `evaluation_results.json`, `confusion_matrix.png`, `confusion_matrix.md`, and an updated version of this README with the metrics filled in.

Required metrics to include after running:

- Baseline accuracy
- Baseline per-class precision, recall, and F1
- Fine-tuned accuracy
- Fine-tuned per-class precision, recall, and F1
- Fine-tuned confusion matrix as a markdown table
- At least 3 wrong predictions with analysis

## Sample Classifications
After fine-tuning, run the "Sample classifications for the README and demo video" cell. It will display 3-5 HN-style comments with predicted labels and confidence scores.

One reasonable correct prediction should be a comment like: "The extra latency is probably from the new auth check. It adds a database lookup on every request..." This should be `evidence_based_reasoning` because it gives a causal explanation rather than only making a judgment.

## Reflection
I intended the model to learn a discourse distinction: supported reasoning versus unsupported assertion versus conversational reply. The model may partly learn surface proxies instead, such as long comments and technical terms for `evidence_based_reasoning`, superlatives for `unsupported_claim`, and question marks or thanks for `conversational_reaction`. The gap is that real HN comments often mix explanation, opinion, and conversation in one reply.

## Spec Reflection
The spec helped by forcing label definitions and edge-case rules before training. The implementation diverged from the original TV-discussion idea because Reddit blocked anonymous automated collection, so I switched to Hacker News where public comments were accessible through a documented API.

## AI Usage
1. I used AI assistance to stress-test and refine the label taxonomy, especially the boundary between technical reasoning and unsupported technical-sounding claims. I kept the generated boundary cases but rewrote the decision rule in my own words.
2. I used AI/rule-assisted labeling to produce the first pass over the collected HN comments. I disclosed this workflow, kept notes in the CSV, and used the labels as training data for the notebook.
3. I used AI assistance to structure the notebook, README, planning document, and demo checklist. I revised the wording to match the actual HN dataset and assignment requirements.

## Demo Video Plan
In the demo, show the sample classification table from the notebook with 3-5 comments, predicted labels, and confidence. Narrate one correct `evidence_based_reasoning` prediction, one incorrect prediction from the wrong-prediction list, and briefly show the baseline-vs-fine-tuned metrics plus the confusion matrix.
