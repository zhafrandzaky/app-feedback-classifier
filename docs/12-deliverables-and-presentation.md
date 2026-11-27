# 12. Deliverables dan Presentasi

## Folder pengumpulan Part 1

Nama folder: `NLP_Part1_Kelompok_02`. Dikumpulkan oleh ketua kelompok.

| Berkas | Isi |
|---|---|
| Laporan `.docx` | Laporan lengkap Part 1 |
| `raw_dataset.csv` | Dataset berlabel, teks asli (kolom `text, label` dan kolom tambahan) |
| `clean_dataset_part1.csv` | Dataset hasil preprocessing (`text, clean_text, label`) |
| `link_dataset.txt` | Tautan sumber data dan Kaggle Dataset |
| Notebook `.ipynb` | Notebook 00 sampai 04 versi akhir dari Kaggle |

## Kerangka laporan

1. Pendahuluan: latar belakang, rumusan masalah, tujuan.
2. Dataset: sumber, metode scraping, filter, pelabelan, distribusi label, contoh data.
3. Exploratory Data Analysis.
4. Preprocessing: tiap tahap dengan tabel sebelum dan sesudah, tabel token, penjelasan regex.
5. Ekstraksi fitur: BoW dan TF-IDF, jumlah fitur, contoh matriks sparse, kelebihan dan kekurangan.
6. Pemodelan: empat model, metrik, confusion matrix, analisis kesalahan.
7. Word embedding: parameter, dimensi, tabel 10 kata, visualisasi.
8. Kesimpulan, keterbatasan, dan rencana Part 2.
9. Lampiran: tautan repositori dan notebook Kaggle.

## Presentasi

- Durasi 5 menit, seluruh anggota berbicara, dilanjutkan 10 menit tanya jawab acak.
- Isi: latar belakang, EDA, alur preprocessing.
- Slide visual dengan potongan kode singkat, bukan halaman teks penuh.
- Grafik diekspor ke `reports/figures/` agar konsisten antara laporan dan slide.

Usulan pembagian waktu:

| Bagian | Durasi |
|---|---|
| Latar belakang dan dataset | 1,5 menit |
| EDA | 1,5 menit |
| Alur preprocessing dengan contoh sebelum dan sesudah | 2 menit |

## Persiapan tanya jawab

Setiap anggota sebaiknya dapat menjawab:

- Mengapa memilih domain e-wallet dan empat aplikasi tersebut?
- Bagaimana pelabelan otomatis bekerja dan apa keterbatasannya?
- Apa fungsi setiap pola regex?
- Mengapa kata negasi tidak dibuang saat stopword removal?
- Apa beda BoW dan TF-IDF, dan bagaimana rumus TF-IDF?
- Mengapa macro F1 lebih tepat daripada accuracy untuk data tidak seimbang?
- Apa arti nilai cosine similarity pada Word2Vec?
