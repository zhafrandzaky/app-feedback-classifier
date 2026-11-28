# Notebooks

Notebook Kaggle untuk Part 1, dijalankan berurutan. Penjelasan alur ada di [docs/08-kaggle-workflow.md](../docs/08-kaggle-workflow.md).

| Notebook | Input | Output | Status |
|---|---|---|---|
| `00_scraping_dataset.ipynb` | Google Play atau `scraped_reviews_all.csv` dan `scraped_reviews_relevant.csv` | `raw_dataset.csv`, `link_dataset.txt` | Selesai ditulis |
| `01_eda_preprocessing.ipynb` | `raw_dataset.csv` | `clean_dataset_part1.csv`, `preprocessing_examples.csv`, `preprocessing_stats.csv`, `typo_corrections.csv`, `figures/` | Selesai ditulis, menunggu Save Version di Kaggle |
| `02_feature_extraction.ipynb` | `clean_dataset_part1.csv` | Matriks BoW dan TF-IDF | Direncanakan |
| `03_classical_ml.ipynb` | Hasil notebook 02 | Metrik dan model terbaik | Direncanakan |
| `04_word_embedding.ipynb` | `clean_dataset_part1.csv` | Model Word2Vec dan visualisasi | Direncanakan |

Notebook Part 2 akan diberi nomor mulai `10_`.
