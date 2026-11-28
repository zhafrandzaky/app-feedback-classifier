# 05. Ekstraksi Fitur

Notebook: `notebooks/02_text_representation.ipynb` (nama Kaggle `02-text-representation-app-feedback`). Input: `clean_dataset_part1.csv` dari dataset Kaggle `app-feedback-ewallet-clean` (Output notebook 01).

Status: selesai ditulis dan diuji lokal dengan data sintetis berukuran sama. Angka hasil (jumlah fitur, sparsity, term teratas) ditulis di sini setelah Save Version Kaggle.

## Pembagian data

- Split stratified 80 persen latih dan 20 persen uji: `train_test_split(test_size=0.2, stratify=label, random_state=42)`.
- Split dihitung satu kali pada seluruh data dan disimpan sebagai kolom `split` di `dataset_split.csv`, bersama `row_id` (nomor baris di `clean_dataset_part1.csv`). Notebook 03 dan model Part 2 memakai file ini sehingga semua model diuji pada data uji yang sama.
- Vectorizer di-fit hanya pada data latih, lalu dipakai untuk mentransformasi data uji. Ini mencegah kebocoran informasi dari data uji.
- Notebook mencatat jumlah ulasan uji yang `clean_text`-nya persis ada di data latih. Baris ini tidak dibuang (ulasan pendek yang sama wajar terjadi) tetapi disebut sebagai keterbatasan di laporan.

## Representasi

| Kode | Representasi | Kelas scikit-learn | Parameter utama |
|---|---|---|---|
| `bow` | Bag of Words | `CountVectorizer` | `token_pattern=r'\S+'`, `ngram_range=(1, 2)`, `min_df=2`, `max_df=0.95`, `lowercase=False` |
| `tfidf_word` | TF-IDF kata | `TfidfVectorizer` | Sama dengan BoW, ditambah `sublinear_tf=True`, `smooth_idf=True`, `norm='l2'` |
| `tfidf_char` | TF-IDF char n-gram | `TfidfVectorizer` | `analyzer='char_wb'`, `ngram_range=(3, 5)`, `min_df=5`, `max_df=0.95`, `sublinear_tf=True`, `norm='l2'` |

Alasan parameter:

- `token_pattern=r'\S+'`: `clean_text` sudah ditokenisasi di notebook 01, jadi cukup dipisah per spasi. Pola bawaan scikit-learn membuang token satu huruf.
- `ngram_range=(1, 2)`: bigram menangkap frasa seperti `tidak bisa` dan `saldo hilang`.
- `min_df=2` (kata) dan `min_df=5` (char): membuang term yang terlalu jarang; batas char lebih tinggi karena jumlah potongan huruf jauh lebih banyak.
- `max_df=0.95`: membuang term yang muncul di hampir semua ulasan.
- `sublinear_tf=True`: tf diganti `1 + ln(tf)` agar kata yang diulang dalam satu ulasan tidak mendominasi.
- `char_wb` dengan 3 sampai 5 huruf: potongan hanya diambil di dalam batas kata, sehingga `eror` dan `error` berbagi banyak potongan dan tetap mirip. Ini menangani typo yang lolos koreksi di notebook 01.

BoW dan TF-IDF kata memakai parameter kosakata yang sama sehingga kosakatanya identik; perbedaannya hanya pada bobot.

Rumus TF-IDF yang dijelaskan di laporan: `tfidf(t, d) = tf(t, d) * idf(t)`, dengan `idf(t) = ln((1 + n) / (1 + df(t))) + 1` (default scikit-learn, `smooth_idf=True`), lalu normalisasi L2.

## Yang ditampilkan di notebook

1. Tabel distribusi label per split.
2. Jumlah fitur BoW (unigram dan bigram) dan pengaruh `ngram_range` serta `min_df` terhadap ukuran kosakata.
3. Contoh representasi sparse untuk 5 ulasan (satu per label): format koordinat `(baris, kolom, term, nilai)` dan potongan matriks ulasan x 10 term, untuk ketiga representasi.
4. Nilai IDF terkecil dan terbesar.
5. Ketahanan typo: cosine similarity pasangan kata benar dan salah ketik pada TF-IDF kata dan char n-gram.
6. Tabel perbandingan: jumlah fitur, nilai bukan nol, sparsity, fitur aktif per ulasan, memori, vektor uji kosong, persentase term uji di luar kosakata, waktu fit.
7. Term teratas per label menurut rata-rata TF-IDF dan chi-square (satu label melawan label lain). Grafiknya menampilkan skor chi-square sepuluh term pembeda teratas per label, sehingga panjang batang sesuai urutan peringkat.
8. Proyeksi 2D `TruncatedSVD` dari TF-IDF kata, diwarnai per label.

## Artefak untuk notebook 03

Notebook 02 menyimpan split dan konfigurasi, bukan matriks atau vectorizer hasil pickle:

| File | Isi |
|---|---|
| `dataset_split.csv` | Seluruh kolom `clean_dataset_part1.csv` ditambah `row_id` dan `split` |
| `representation_config.json` | Parameter ketiga vectorizer, `random_state`, `test_size`, kolom teks, urutan `LABELS` |
| `feature_summary.csv`, `top_terms_per_label.csv`, `sparse_examples.csv`, `typo_similarity.csv` | Tabel untuk laporan |
| `figures/*.png` | Grafik 200 dpi |

Alasannya: notebook 03 memakai `Pipeline` (vectorizer + model) yang di-fit ulang pada data latih dengan parameter yang sama, sehingga hasilnya identik dan pipeline dapat langsung disimpan untuk aplikasi Part 2. Fitting vectorizer hanya butuh beberapa detik, dan CSV atau JSON tidak bergantung pada versi scikit-learn seperti file pickle.

## Kelebihan dan kekurangan

| Aspek | Bag of Words | TF-IDF kata | TF-IDF char n-gram |
|---|---|---|---|
| Kesederhanaan | Paling sederhana | Satu langkah tambahan (IDF) | Sederhana, tetapi fitur sulit dibaca manusia |
| Kata umum | Mendominasi | Bobot diturunkan | Bobot diturunkan |
| Panjang ulasan | Ulasan panjang bernilai lebih besar | Dinetralkan normalisasi L2 | Dinetralkan normalisasi L2 |
| Typo dan variasi ejaan | Dianggap kata berbeda | Dianggap kata berbeda | Tahan typo |
| Urutan kata | Hanya lewat bigram | Hanya lewat bigram | Hilang antar kata |
| Makna dan sinonim | Tidak tertangkap | Tidak tertangkap | Tidak tertangkap |
| Dimensi dan biaya | Tinggi, sparse | Sama dengan BoW | Paling tinggi dan paling lambat |

Keterbatasan makna dan sinonim menjadi motivasi notebook 04 (word embedding Word2Vec dan FastText).
