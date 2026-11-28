# 08. Alur Kerja Kaggle

Seluruh notebook dijalankan di Kaggle agar lingkungan sama untuk semua anggota dan output dapat dibagikan sebagai Input notebook berikutnya.

## Prinsip: tanpa mengubah kode

Kode notebook tidak pernah diubah antara Run All dan Save Version. Dua hal ditentukan otomatis di sel konfigurasi.

### Mode run

Kaggle mengisi variabel lingkungan `KAGGLE_KERNEL_RUN_TYPE`:

| Nilai | Kapan | Perilaku notebook |
|---|---|---|
| `Interactive` | Run All di editor notebook (juga dianggap begitu saat dijalankan lokal) | Smoke test: data kecil, sedikit iterasi, untuk memastikan semua sel berjalan tanpa error |
| `Batch` | Save Version (Save and Run All) | Full run: data dan iterasi penuh, hasilnya menjadi output resmi |

```python
import os
RUN_TYPE = os.environ.get('KAGGLE_KERNEL_RUN_TYPE', 'Interactive')
SMOKE_TEST = RUN_TYPE != 'Batch'
EPOCHS = 1 if SMOKE_TEST else 5
```

Setiap konstanta yang menentukan lama proses ditulis berpasangan seperti contoh di atas, dan mode yang aktif dicetak di awal notebook.

### Sumber data

Notebook mencari file yang dibutuhkan di `/kaggle/input` dengan `glob`. Jika ada, file itu dipakai. Jika tidak ada dan notebook mampu membuatnya (seperti scraping di notebook 00), notebook membuatnya sendiri. Jika tidak bisa, notebook berhenti dengan pesan yang jelas tentang Input yang harus ditambahkan.

## Prasyarat

- Akun Kaggle terverifikasi nomor telepon (dibutuhkan untuk menyalakan Internet).
- Accelerator: None untuk Part 1. GPU untuk Transformer di Part 2.

## Urutan

```
00_scraping_dataset      Save Version (scraping penuh di dalam versi)
        |                -> raw_dataset.csv, scraped_reviews_all.csv, scraped_reviews_relevant.csv, link_dataset.txt
        |                -> Output dijadikan Kaggle Dataset
        v
01_eda_preprocessing     -> clean_dataset_part1.csv
        |
        v
02_text_representation  -> dataset_split.csv (split latih dan uji), representation_config.json
        |
        v
03_classical_ml          -> metrik, confusion matrix, model terbaik
        |
04_word_embedding        -> model Word2Vec, tabel kemiripan, plot
```

## Langkah notebook 00

1. Impor `notebooks/00_scraping_dataset.ipynb` ke Kaggle (File, Import Notebook, bisa dari tautan GitHub).
2. Settings, Internet: On.
3. Opsional: Run All sebagai smoke test. Notebook melakukan scraping kecil (200 ulasan per bintang per aplikasi) dan menjalankan semua tahap.
4. Save Version, Save and Run All (Commit). Scraping penuh (8.000 ulasan per bintang per aplikasi) berjalan di versi ini dan dapat memakan waktu cukup lama.
5. Setelah versi selesai, buka tab Output versi tersebut, lalu New Dataset (misalnya `ewallet-reviews-kelompok2`). Alternatifnya, Output notebook dapat langsung ditambahkan sebagai Input di notebook lain (Add Input, Notebooks).
6. Laporkan distribusi label dari output ke tim dan catat di `docs/02-dataset.md`.

Jika notebook 00 perlu dijalankan ulang (misalnya kamus kata kunci diperbaiki) tanpa scraping ulang, tambahkan dataset dari langkah 5 sebagai Input. Notebook otomatis membaca `scraped_reviews_all.csv` dan `scraped_reviews_relevant.csv` dari Input dan hanya melakukan scraping untuk file yang belum ada.

## Langkah notebook 01

1. Impor `notebooks/01_eda_preprocessing.ipynb` ke notebook Kaggle `01-eda-preprocessing-app-feedback` (File, Import Notebook).
2. Add Input, Datasets, pilih `app-feedback-ewallet-reviews`. Notebook mencari `raw_dataset.csv` secara rekursif di `/kaggle/input`, jadi nama folder tidak berpengaruh.
3. Settings, Internet: On, karena `Sastrawi` (dan `rapidfuzz` bila belum ada) dipasang dengan `pip` di dalam notebook.
4. Opsional: Run All sebagai smoke test (sampel berstrata 3.000 ulasan).
5. Save Version (Save and Run All). Seluruh ulasan diproses; seluruh notebook selesai dalam beberapa menit.
6. Output berisi `clean_dataset_part1.csv`, `raw_dataset.csv`, `preprocessing_examples.csv`, `preprocessing_stats.csv`, `typo_corrections.csv`, dan folder `figures/`. Output ini menjadi Input notebook 02 dan 04.

## Langkah notebook 02

1. Impor `notebooks/02_text_representation.ipynb` ke notebook Kaggle `02-text-representation-app-feedback` (File, Import Notebook).
2. Add Input, Datasets, pilih `app-feedback-ewallet-clean` (Output notebook 01). Notebook mencari `clean_dataset_part1.csv` secara rekursif di `/kaggle/input`.
3. Internet tidak diperlukan; seluruh library sudah tersedia di Kaggle.
4. Opsional: Run All sebagai smoke test. Split tetap dihitung pada seluruh data, tetapi vectorizer hanya di-fit pada sampel berstrata 3.000 ulasan latih dan 750 ulasan uji.
5. Save Version (Save and Run All). Seluruh data latih dan uji dipakai; notebook selesai dalam hitungan menit.
6. Output berisi `dataset_split.csv`, `representation_config.json`, `feature_summary.csv`, `top_terms_per_label.csv`, `sparse_examples.csv`, `typo_similarity.csv`, dan folder `figures/`. `dataset_split.csv` dan `representation_config.json` menjadi Input notebook 03.

## Menghubungkan notebook berikutnya

- Tambahkan dataset atau Output notebook sebelumnya sebagai Input.
- Path input dicari dengan `glob` (contoh: `glob.glob('/kaggle/input/**/raw_dataset.csv', recursive=True)`) agar tidak bergantung pada nama folder.
- Setiap notebook menulis output ke `/kaggle/working`. File perantara yang dipakai ulang ikut disalin ke Output agar setiap versi lengkap.

## Notebook yang lama (Part 2)

Untuk pelatihan yang memakan waktu lama, seperti fine-tuning Transformer:

1. Run All di editor sebagai smoke test (subset data, satu epoch atau beberapa langkah). Pastikan pelatihan, evaluasi, grafik, dan penyimpanan model berjalan.
2. Jika smoke test berhasil, Save Version untuk full run. Kode tidak diubah.
3. Angka resmi untuk laporan hanya diambil dari Save Version.

## Aturan Save Version

- Output resmi selalu dari Save Version agar urutan eksekusi sel bersih dan hasil tersimpan.
- Tidak ada sel yang menunggu input pengguna.
- Catat nomor versi notebook yang dipakai di laporan.

## Sinkronisasi dengan repositori

1. Setelah versi Kaggle stabil, unduh notebook (File, Download Notebook).
2. Simpan ke `notebooks/` dengan nama yang sama, hapus output yang sangat panjang.
3. Commit dengan pesan Conventional Commits, misalnya `feat(eda): add eda and preprocessing notebook`.
