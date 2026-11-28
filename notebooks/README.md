# Notebooks

Notebook Kaggle untuk Part 1, dijalankan berurutan. Penjelasan alur ada di [docs/08-kaggle-workflow.md](../docs/08-kaggle-workflow.md).

| Notebook | Input | Output | Status |
|---|---|---|---|
| `00_scraping_dataset.ipynb` | Google Play atau `scraped_reviews_all.csv` dan `scraped_reviews_relevant.csv` | `raw_dataset.csv`, `link_dataset.txt` | Selesai ditulis |
| `01_eda_preprocessing.ipynb` | `raw_dataset.csv` | `clean_dataset_part1.csv`, `preprocessing_examples.csv`, `preprocessing_stats.csv`, `typo_corrections.csv`, `figures/` | Selesai ditulis, menunggu Save Version di Kaggle |
| `02_text_representation.ipynb` | `clean_dataset_part1.csv` | `dataset_split.csv`, `representation_config.json`, `feature_summary.csv`, `top_terms_per_label.csv`, `sparse_examples.csv`, `typo_similarity.csv`, `figures/` | Selesai ditulis, menunggu Save Version di Kaggle |
| `03_classical_ml.ipynb` | `dataset_split.csv`, `representation_config.json` | Metrik dan model terbaik | Direncanakan |
| `04_word_embedding.ipynb` | `clean_dataset_part1.csv` | Model Word2Vec dan visualisasi | Direncanakan |

Notebook Part 2 akan diberi nomor mulai `10_`.
