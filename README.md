# App Feedback Classifier

Klasifikasi teks ulasan pengguna aplikasi dompet digital (e-wallet) Indonesia ke dalam lima kategori masukan pengembangan aplikasi.

Proyek akhir Praktikum Natural Language Processing, Kelompok 2.

Anggota:

- Muhammad Zhafran Dzaky
- Lufiana
- Muh. Farhan Ramadhan

Pembagian tugas dan jadwal ada di [docs/10-team-and-timeline.md](docs/10-team-and-timeline.md).

## Ringkasan

Masukan pengguna di toko aplikasi berisi informasi berharga bagi tim pengembang, tetapi jumlahnya terlalu banyak untuk dibaca manual. Proyek ini membangun model yang memetakan setiap ulasan ke salah satu kategori berikut:

| Label | Isi masukan |
|---|---|
| Permintaan Fitur | Usulan fitur, menu, atau opsi baru |
| Bug Aplikasi | Error, crash, fungsi tidak berjalan sebagaimana mestinya |
| Desain Antarmuka | Tampilan, tata letak, navigasi, kemudahan penggunaan |
| Performa Aplikasi | Lambat, lemot, loading lama, boros baterai atau memori |
| Keamanan Aplikasi | Akun dibobol, penipuan, OTP, PIN, verifikasi, privasi data |

Sumber data adalah ulasan Google Play untuk empat aplikasi: DANA, GoPay, OVO, dan ShopeePay. Dataset dikumpulkan sendiri melalui scraping lalu dilabeli dengan aturan kata kunci berbobot yang diperiksa manual.

## Struktur repositori

```
app-feedback-classifier/
|-- notebooks/          Notebook Kaggle, berurutan 00 sampai 04 (Part 1)
|-- data/               Penjelasan dataset; file CSV besar tidak di-commit
|   |-- raw/            raw_dataset.csv (text, label, app, rating, date)
|   `-- processed/      clean_dataset_part1.csv (text, clean_text, label)
|-- models/             Artefak model terlatih (tidak di-commit)
|-- reports/figures/    Grafik untuk laporan dan presentasi
|-- docs/               Dokumentasi proyek lengkap
|-- app/                Aplikasi web atau mobile (Part 2, belum dimulai)
|-- .claude/skills/     Panduan kerja untuk asisten AI di repo ini
|-- CLAUDE.md           Aturan repositori
`-- requirements.txt    Dependensi untuk menjalankan notebook secara lokal
```

## Pipeline Part 1

| No | Notebook | Isi | Status |
|---|---|---|---|
| 00 | `00_scraping_dataset.ipynb` | Scraping ulasan Google Play, filter, pelabelan berbasis kata kunci | Selesai, menunggu uji di Kaggle |
| 01 | `01_eda_preprocessing.ipynb` | EDA, case folding, regex cleaning, tokenisasi, stopword, stemming, normalisasi slang | Selesai ditulis, menunggu Save Version di Kaggle |
| 02 | `02_feature_extraction.ipynb` | Bag of Words dan TF-IDF | Direncanakan |
| 03 | `03_classical_ml.ipynb` | Naive Bayes, SVM, Logistic Regression, Random Forest | Direncanakan |
| 04 | `04_word_embedding.ipynb` | Word2Vec, kemiripan kata, visualisasi PCA atau t-SNE | Direncanakan |

Penjelasan tiap tahap ada di [docs/](docs/README.md).

## Cara menjalankan

Semua notebook dirancang untuk Kaggle.

1. Buat notebook baru di Kaggle, lalu impor file dari folder `notebooks/` (File, Import Notebook).
2. Untuk notebook 00, aktifkan Internet (Settings, Internet on).
3. Opsional: Run All di editor sebagai smoke test singkat (data kecil).
4. Save Version (Save and Run All) untuk full run. Kode tidak perlu diubah; notebook mendeteksi mode dari `KAGGLE_KERNEL_RUN_TYPE`.
5. Jadikan output versi tersebut Kaggle Dataset (tab Output, New Dataset) atau tambahkan langsung sebagai Input notebook berikutnya.

Detail alur kerja Kaggle ada di [docs/08-kaggle-workflow.md](docs/08-kaggle-workflow.md).

Untuk menjalankan secara lokal:

```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook notebooks/
```

Path `/kaggle/input` dan `/kaggle/working` di sel konfigurasi perlu diganti ke `data/raw` dan `data/processed` saat dijalankan lokal.

## Rencana Part 2

Part 2 belum dikonfirmasi asisten praktikum. Perkiraan cakupannya adalah model deep learning dan Transformer (misalnya IndoBERT), lalu aplikasi web atau mobile yang memakai model tersebut. Pilihan framework (Next.js TypeScript, Flutter, atau React Native TypeScript) belum ditentukan. Lihat [docs/09-part2-roadmap.md](docs/09-part2-roadmap.md).

## Kontribusi

Aturan commit, branch, dan gaya penulisan ada di [CLAUDE.md](CLAUDE.md) dan [docs/11-contributing.md](docs/11-contributing.md).

## Lisensi dan etika data

Repositori ini dibuat untuk keperluan akademik. Ulasan diambil dari halaman publik Google Play dan hanya teks ulasan, rating, tanggal, dan nama aplikasi yang disimpan. Nama pengguna dan identitas pengulas tidak disimpan di dataset final. Lihat [docs/02-dataset.md](docs/02-dataset.md).
