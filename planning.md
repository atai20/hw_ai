# TakeMeter Planning

## Community
I chose public Hacker News discussion comments because HN is text-heavy, public, and built around debate. The discourse is varied enough for classification: some comments explain a technical or social claim with evidence, some make broad unsupported claims, and some are short conversational replies that keep a thread moving but do not function as arguments.

## Labels
- `evidence_based_reasoning`: The comment makes a claim and supports it with concrete reasoning, technical detail, examples, data, causal explanation, or first-hand experience.
  - Example: "The extra latency is probably from the new auth check. It adds a database lookup on every request, so even a 10ms query becomes visible at p95 under load."
  - Example: "I used this queue pattern at work; it simplified the code, but incident response got harder because state was split across two systems."
- `unsupported_claim`: The comment makes a confident judgment, prediction, or broad claim without enough evidence or reasoning inside the comment to support it.
  - Example: "This startup is obviously doomed and nobody serious will use it."
  - Example: "Rust is just better than Go for everything serious."
- `conversational_reaction`: The comment is mainly a short reply, question, joke, agreement, clarification request, or emotional reaction rather than a developed argument.
  - Example: "Interesting, do you have a link to the benchmark?"
  - Example: "Thanks, that explanation helped."

## Hard Edge Cases
The hardest boundary is between `evidence_based_reasoning` and `unsupported_claim` when a comment uses technical vocabulary but does not really connect that detail to the claim. My rule is to label it `evidence_based_reasoning` only if the detail supports the conclusion through an example, causal explanation, data point, tradeoff, or direct experience. If the comment mostly asserts a conclusion, it is `unsupported_claim`.

Three difficult labeling cases:

1. A short comment asking for a source on a technical claim could look analytical, but I label it `conversational_reaction` because it is a thread-management reply rather than a developed argument.
2. A long comment with many confident claims but no concrete evidence remains `unsupported_claim`; length alone is not enough.
3. A personal anecdote can be `evidence_based_reasoning` if it explains what happened and why it matters, but `unsupported_claim` if it only says something was good or bad.

## Data Collection Plan
I collected real public comments through the Hacker News Algolia API and saved them in `takemeter_hn_discourse_dataset.csv`. The CSV includes `text`, `label`, `notes`, `source`, and `object_id`. The initial labels were produced with an AI/rule-assisted pass using the definitions above. Label distribution: 80 `evidence_based_reasoning`, 80 `unsupported_claim`, and 78 `conversational_reaction`. No label is above 70 percent of the dataset.

## Evaluation Metrics
I will use accuracy to compare the fine-tuned DistilBERT model against the Groq zero-shot baseline, macro F1 to treat all labels equally, and per-class precision/recall/F1 to identify which boundary is hardest. The confusion matrix matters because the most important error is confusing supported reasoning with unsupported claims.

## Definition of Success
A useful classifier should beat the Groq zero-shot baseline and reach at least 0.70 macro F1 on the held-out test set. For real deployment, I would want `evidence_based_reasoning` precision above 0.75 so the tool does not incorrectly promote unsupported claims as substantive comments.

## AI Tool Plan
For label stress-testing, I used AI to generate boundary cases between technical reasoning, unsupported claims, and conversational replies, then tightened the rule around whether evidence actually supports a claim. For annotation assistance, I used a transparent AI/rule-assisted first pass and documented the rule in the CSV notes. For failure analysis, I will use the model's wrong predictions to identify recurring error patterns and then verify those patterns by rereading examples.
