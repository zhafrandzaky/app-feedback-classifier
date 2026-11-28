# 05. Ekstraksi Fitur

Notebook: `notebooks/02_feature_extraction.ipynb` (direncanakan). Input: `clean_dataset_part1.csv`.

## Pembagian data

- Split stratified 80 persen latih dan 20 persen uji, `random_state = 42`.
- Vectorizer di-fit hanya pada data latih, lalu dipakai untuk mentransformasi data uji. Ini mencegah kebocoran informasi dari data uji.
- Indeks split disimpan agar notebook 03 memakai pembagian yang sama.

## Bag of Words

- `CountVectorizer` dari scikit-learn.
- Setiap dokumen direpresentasikan sebagai vektor jumlah kemunculan kata dalam kosakata.
- Parameter yang dicatat: `ngram_range`, `min_df`, `max_df`, `max_features`.

## TF-IDF

- `TfidfVectorizer` dari scikit-learn.
- Bobot kata = frekuensi kata dalam dokumen dikali kebalikan frekuensi dokumen yang memuatnya. Kata yang muncul di hampir semua ulasan (misalnya `aplikasi`) mendapat bobot kecil.
- Rumus yang dijelaskan di laporan: `tfidf(t, d) = tf(t, d) * idf(t)`, dengan `idf(t) = ln((1 + n) / (1 + df(t))) + 1` (default scikit-learn, `smooth_idf=True`), lalu normalisasi L2.

- Rencana tambahan: TF-IDF berbasis potongan huruf (`analyzer='char_wb'`, misalnya `ngram_range=(3, 5)`) sebagai pembanding TF-IDF kata. Potongan huruf seperti `err` dan `ror` membuat `eror` dan `error` tetap mirip, sehingga typo yang lolos koreksi di notebook 01 masih dikenali.

## Yang ditampilkan

1. Jumlah fitur (ukuran kosakata) untuk BoW dan TF-IDF, unigram dan bigram.
2. Bentuk matriks dan tingkat sparsity (persentase nol).
3. Contoh potongan matriks sparse untuk 5 dokumen dan 10 fitur sebagai DataFrame.
4. Kata dengan bobot TF-IDF rata-rata tertinggi per label.

## Kelebihan dan kekurangan

| Aspek | Bag of Words | TF-IDF |
|---|---|---|
| Kesederhanaan | Sangat sederhana, mudah dijelaskan | Sederhana, satu langkah tambahan |
| Kata umum | Kata umum mendominasi | Kata umum diturunkan bobotnya |
| Kata khas | Tidak dibedakan | Kata khas dokumen mendapat bobot tinggi |
| Urutan kata | Hilang (kecuali dengan n-gram) | Hilang (kecuali dengan n-gram) |
| Makna dan sinonim | Tidak tertangkap | Tidak tertangkap |
| Dimensi | Tinggi dan sparse | Tinggi dan sparse |

Keterbatasan makna dan sinonim menjadi motivasi notebook 04 (word embedding).
