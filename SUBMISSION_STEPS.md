# Submission Steps

## What Is Already Prepared
- `ai201_project3_takemeter_starter_clean.ipynb`: completed notebook configured for the HN dataset.
- `takemeter_hn_discourse_dataset.csv`: 238 public Hacker News comments with labels, notes, and source URLs.
- `planning.md`: project planning document.
- `README.md`: project report draft.

## What You Need To Run
1. Upload/open `ai201_project3_takemeter_starter_clean.ipynb` in Google Colab.
2. Upload `takemeter_hn_discourse_dataset.csv` when the notebook asks for it.
3. Set runtime to T4 GPU: `Runtime -> Change runtime type -> T4 GPU`.
4. Add your Groq key to `.env`:
   - Open `.env`.
   - Replace `your_actual_groq_key_here` with your real Groq API key.
   - In Colab, upload `.env` when the Groq baseline cell asks for it.
   - Do not commit `.env`; `.gitignore` already excludes it.
5. Run every notebook cell from top to bottom.
6. Download the generated files:
   - `evaluation_results.json`
   - `confusion_matrix.png`
   - `confusion_matrix.md`
   - updated `planning.md`
   - updated `README.md`

## Demo Video Checklist
Record 3-5 minutes and show:

1. The notebook sample-classification table with 3-5 comments, predicted labels, and confidence scores.
2. One correct prediction. Recommended narration: explain why a latency/tradeoff comment is `evidence_based_reasoning` because it gives a causal mechanism.
3. One incorrect prediction from the wrong-prediction cell. Explain which two labels were confused and why the boundary is hard.
4. The evaluation report section showing baseline accuracy, fine-tuned accuracy, per-class metrics, and the confusion matrix.
5. Briefly mention that the dataset is public HN comments and that the labels were AI/rule-assisted with notes included in the CSV.

## Submit
Submit your GitHub repo link plus the demo video through the Course Portal. The repo should include the notebook, CSV, README, planning document, and generated evaluation files.
