# 06. Model Machine Learning Klasik

Notebook: `notebooks/03_classical_ml.ipynb` (direncanakan).

## Model

| Model | Kelas scikit-learn | Catatan |
|---|---|---|
| Multinomial Naive Bayes | `MultinomialNB` | Baseline cepat, cocok untuk fitur hitungan dan TF-IDF |
| Support Vector Machine | `LinearSVC` | Kuat untuk teks berdimensi tinggi, `class_weight='balanced'` |
| Logistic Regression | `LogisticRegression` | Probabilitas kelas, koefisien mudah ditafsirkan, `class_weight='balanced'`, `max_iter=1000` |
| Random Forest | `RandomForestClassifier` | Pembanding non-linear, `class_weight='balanced'`, `n_estimators` dicatat |

Setiap model dilatih dengan fitur BoW dan TF-IDF, sehingga ada delapan kombinasi yang dibandingkan.

## Penanganan ketidakseimbangan kelas

- `class_weight='balanced'` untuk SVM, Logistic Regression, dan Random Forest.
- Naive Bayes tidak memiliki `class_weight`; prior kelas dapat diatur manual atau dibiarkan dan dibahas di analisis.
- Metrik utama perbandingan adalah macro F1, karena accuracy dapat tinggi hanya dengan menebak kelas mayoritas.

## Metrik

- Accuracy.
- Precision, recall, F1-score per kelas (`classification_report`).
- Macro average dan weighted average.
- Confusion matrix untuk setiap model terbaik per fitur, divisualisasikan sebagai heatmap dengan nama label.

## Analisis yang dituliskan

1. Tabel ringkasan seluruh kombinasi model dan fitur, diurutkan berdasarkan macro F1.
2. Kelas yang paling sering tertukar menurut confusion matrix, beserta penjelasan linguistiknya (contoh: Bug dan Performa sama-sama memuat "loading").
3. Contoh salah klasifikasi: teks, label asli, prediksi, dan dugaan penyebab.
4. Fitur paling berpengaruh per kelas dari koefisien Logistic Regression atau SVM.
5. Catatan bahwa label berasal dari kata kunci, sehingga skor tinggi sebagian mencerminkan aturan pelabelan.

## Opsi tambahan

- Validasi silang stratified 5 lipatan untuk model terbaik.
- Pencarian hyperparameter kecil dengan `GridSearchCV` (misalnya `C` untuk SVM dan Logistic Regression).
- Menyimpan pipeline terbaik dengan `joblib` sebagai bahan Part 2. File model tidak di-commit.
