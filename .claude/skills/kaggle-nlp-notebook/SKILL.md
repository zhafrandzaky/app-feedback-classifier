---
name: kaggle-nlp-notebook
description: How to write or edit the Kaggle Jupyter notebooks (00 to 04 and later Part 2) for this Indonesian text classification project, so they run end to end, are reproducible, and can be explained by every team member. Use when creating or modifying any .ipynb in notebooks/.
---

# Kaggle NLP Notebook Authoring

## Audience

The notebooks are graded and every team member is questioned at random about any cell. Clarity beats cleverness. Markdown is formal Indonesian. Identifiers are English `snake_case`.

## Required structure

1. Title cell: `# NN · Judul`, course and group line, case study line, a table of the steps.
2. Configuration cell: all constants in one place.
   ```python
   RANDOM_STATE = 42
   INPUT_DIR = '/kaggle/input'
   OUTPUT_DIR = '/kaggle/working'
   LABELS = ['Permintaan Fitur', 'Bug Aplikasi', 'Desain Antarmuka', 'Performa Aplikasi', 'Keamanan Aplikasi']
   ```
3. Import cell. Install only what Kaggle lacks (`google-play-scraper`, `Sastrawi`) with `!pip install -q`.
4. Numbered sections (`## 1. ...`). Each code cell is preceded by a markdown cell that states what the cell does and why.
5. A closing cell that names the output files and the next notebook.

## Reproducibility

- Set `random_state` / `seed` everywhere: `train_test_split`, `sample`, models, Word2Vec (`seed=42, workers=1`).
- Locate inputs with `glob.glob(f'{INPUT_DIR}/**/<file>.csv', recursive=True)` and fail with a clear message if not found.
- Never require a code edit between Run All and Save Version. No manual flags.
- Detect the run mode in the configuration cell:
  ```python
  import os
  RUN_TYPE = os.environ.get('KAGGLE_KERNEL_RUN_TYPE', 'Interactive')   # 'Interactive' = editor or local, 'Batch' = Save Version
  SMOKE_TEST = RUN_TYPE != 'Batch'
  print(f'RUN_TYPE: {RUN_TYPE} -> {"SMOKE TEST" if SMOKE_TEST else "FULL RUN"}')
  ```
- Every expensive size has a smoke value and a full value next to each other, for example:
  ```python
  SAMPLE_SIZE = 2000 if SMOKE_TEST else None     # None = all rows
  EPOCHS = 1 if SMOKE_TEST else 5
  MAX_STEPS = 50 if SMOKE_TEST else -1            # Hugging Face Trainer
  N_ESTIMATORS = 50 if SMOKE_TEST else 300
  ```
  The smoke test must exercise every cell (training, evaluation, plots, saving) on the small setting, so a passing Run All means Save Version will not crash.
- Inputs are detected automatically: search with `glob` under `INPUT_DIR`. If an expected intermediate file exists, reuse it; otherwise produce it (notebook 00 scrapes only when `scraped_reviews_all.csv` is not already an input). Fail with a clear Indonesian message when a required input is missing.
- Copy reused intermediate files into `OUTPUT_DIR` so each version's output is self-contained and can become the next Kaggle Dataset.
- Official results (numbers in docs and the report) come only from a Save Version (`Batch`) run.
- Do not keep state that depends on cell execution order. Run All from a fresh kernel must work.

## Output for the report

- Show tables as DataFrames, not raw prints, so they can be copied into the report.
- Plots: one message per figure, titled in Indonesian, axes labelled, label names on categorical axes, `plt.tight_layout()`. Save figures to `OUTPUT_DIR/figures/*.png` at 200 dpi when they are needed for the report or slides.
- Use a consistent color per label across all notebooks. Define it once in the configuration cell.
- Keep printed output short. Print summaries, not entire datasets.

## Required content per notebook (Part 1)

| Notebook | Must show |
|---|---|
| 00 | Scraping summary per app and star, filter counts, labeling summary, label distribution, manual sample check |
| 01 | EDA, before/after table for each preprocessing step, token table, regex explanation, `clean_dataset_part1.csv` |
| 02 | Stratified split, BoW and TF-IDF feature counts, sparse matrix sample, sparsity, pros and cons |
| 03 | NB, SVM, LR, RF on both features, accuracy, per-class P/R/F1, macro F1 ranking, confusion matrices, error analysis |
| 04 | Word2Vec parameters, vocabulary size and dimension, 10-word similarity table, PCA or t-SNE plot, analysis |

See `docs/04-preprocessing.md` to `docs/07-word-embedding.md` for the detailed plan of each notebook.

## Style

- No emoji anywhere. Emoji inside data examples are written as Unicode escapes.
- Short functions with docstrings for anything reused. Avoid deep nesting in notebook cells.
- Explain every regex pattern in a markdown table: pattern, meaning, example before, example after.

## Before handing over

- Run All in a fresh kernel as the smoke test (or state clearly that it has not been run and why).
- Clear very long outputs. Keep compact tables and figures.
- Update `notebooks/README.md` status and the matching doc in `docs/`.
