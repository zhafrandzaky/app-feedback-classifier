# 08. Alur Kerja Kaggle

Seluruh notebook dijalankan di Kaggle agar lingkungan sama untuk semua anggota dan output dapat dibagikan sebagai Input notebook berikutnya.

## Prasyarat

- Akun Kaggle terverifikasi nomor telepon (dibutuhkan untuk menyalakan Internet).
- Accelerator: None (CPU cukup untuk Part 1).

## Urutan

```
00_scraping_dataset      SCRAPE=True sekali  -> scraped_reviews_all.csv
        |                                         diunggah sebagai Kaggle Dataset
        |                 SCRAPE=False, Save Version
        v
   raw_dataset.csv, link_dataset.txt  (Output notebook 00)
        |
        v
01_eda_preprocessing     -> clean_dataset_part1.csv
        |
        v
02_feature_extraction    -> matriks BoW dan TF-IDF, indeks split
        |
        v
03_classical_ml          -> metrik, confusion matrix, model terbaik
        |
04_word_embedding        -> model Word2Vec, tabel kemiripan, plot
```

## Langkah rinci notebook 00

1. Impor `notebooks/00_scraping_dataset.ipynb` ke Kaggle.
2. Settings, Internet: On.
3. Ubah `SCRAPE = True`, jalankan semua sel secara interaktif. Proses dapat memakan waktu lama karena target 8.000 ulasan per bintang per aplikasi.
4. Unduh `/kaggle/working/scraped_reviews_all.csv`.
5. Buat Kaggle Dataset baru (misalnya `ewallet-reviews-raw`) dan unggah file tersebut.
6. Tambahkan dataset itu sebagai Input notebook (Add Input).
7. Ubah `SCRAPE = False`, lalu Save Version, Save and Run All.
8. Laporkan distribusi label dari output ke tim dan catat di `docs/02-dataset.md`.

## Menghubungkan notebook berikutnya

- Notebook 01 menambahkan Output notebook 00 sebagai Input (Add Input, Notebooks).
- Path input dicari dengan `glob` (contoh: `glob.glob('/kaggle/input/**/raw_dataset.csv', recursive=True)`) agar tidak bergantung pada nama folder.
- Setiap notebook menulis output ke `/kaggle/working`.

## Aturan Save Version

- Selalu Save and Run All sebelum mengumpulkan, agar urutan eksekusi sel bersih dan output tersimpan.
- Tidak ada sel yang menunggu input pengguna.
- Catat nomor versi notebook yang dipakai di laporan.

## Sinkronisasi dengan repositori

1. Setelah versi Kaggle stabil, unduh notebook (File, Download Notebook).
2. Simpan ke `notebooks/` dengan nama yang sama, hapus output yang sangat panjang.
3. Commit dengan pesan Conventional Commits, misalnya `feat(eda): add eda and preprocessing notebook`.
