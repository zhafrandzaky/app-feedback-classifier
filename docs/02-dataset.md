# 02. Dataset

## Sumber

Ulasan publik Google Play Store, bahasa `id`, negara `id`.

| Aplikasi | Package id | Halaman |
|---|---|---|
| DANA | `id.dana` | https://play.google.com/store/apps/details?id=id.dana |
| GoPay | `com.gojek.gopay` | https://play.google.com/store/apps/details?id=com.gojek.gopay |
| OVO | `ovo.id` | https://play.google.com/store/apps/details?id=ovo.id |
| ShopeePay | `com.shopeepay.id` | https://play.google.com/store/apps/details?id=com.shopeepay.id |

Package id diverifikasi ulang di notebook 00 melalui `resolve_app_id`. Jika package id tidak ditemukan, notebook mencari aplikasi dengan `search()` dan memakai hasil yang judulnya cocok. Package id final dicatat di `link_dataset.txt`.

## Metode pengambilan

Notebook: `notebooks/00_scraping_dataset.ipynb`.

- Library: [`google-play-scraper`](https://pypi.org/project/google-play-scraper/) (tidak resmi).
- Ulasan diambil per bintang 1 sampai 5 dengan `filter_score_with` dari dua urutan, `Sort.NEWEST` (ulasan terbaru) dan `Sort.MOST_RELEVANT` (ulasan paling relevan menurut Google Play), masing-masing target `REVIEWS_PER_STAR = 8000` per aplikasi per bintang. Pengambilan per bintang menjaga keragaman topik: bintang rendah kaya keluhan bug, performa, dan keamanan, sedangkan bintang tinggi lebih sering memuat pujian tampilan dan usulan fitur. Urutan relevan ditambahkan setelah versi 1 karena ulasannya lebih panjang dan lebih sering berisi usulan fitur dan penilaian tampilan. Ulasan yang muncul di kedua urutan hanya diambil sekali (berdasarkan `review_id`).
- Google Play mengembalikan sekitar 200 ulasan per permintaan, sehingga notebook mengulang dengan `continuation_token` sampai target tercapai atau ulasan habis.
- Jeda `time.sleep(0.3)` antar permintaan agar tidak membebani server.
- Tanpa flag manual. `scraped_reviews_all.csv` (terbaru) dan `scraped_reviews_relevant.csv` (relevan) dicek terpisah di Input Kaggle. File yang sudah ada dibaca, file yang belum ada di-scraping (butuh Internet di Kaggle).
- Mode run otomatis dari `KAGGLE_KERNEL_RUN_TYPE`: Run All di editor (`Interactive`) adalah smoke test dengan 200 ulasan per bintang per aplikasi, Save Version (`Batch`) adalah full run dengan 8.000 ulasan per bintang per aplikasi. Dataset resmi selalu berasal dari Save Version.

## Filter awal

1. Buang ulasan kosong.
2. Rapikan spasi dan baris baru (isi teks tidak diubah).
3. Buang ulasan kurang dari 3 kata (`MIN_WORDS = 3`) karena terlalu pendek untuk menunjukkan topik.
4. Buang duplikat berdasarkan teks huruf kecil.

## Pelabelan

Pelabelan otomatis berbasis kata kunci berbobot, lalu diperiksa manual per label. Ulasan yang tidak relevan atau ambigu dibuang. Detail di [03-labeling-guidelines.md](03-labeling-guidelines.md).

## Kebijakan ukuran dan keseimbangan

- Semua ulasan yang lolos pelabelan dipakai. Tidak ada batas maksimal dan tidak ada downsampling paksa, supaya data tidak terbuang.
- `MAX_PER_LABEL = None` tersedia sebagai opsi jika suatu saat diperlukan pembatasan.
- Ketidakseimbangan kelas ditangani di tahap pemodelan (notebook 03) dengan `class_weight='balanced'` dan evaluasi per kelas (precision, recall, F1, macro average).

### Hasil pelabelan

| Label | Versi 1 Kaggle (aturan awal, terbaru saja) | Aturan revisi, terbaru saja (simulasi lokal) | Aturan revisi, terbaru + relevan (simulasi lokal) |
|---|---:|---:|---:|
| Bug Aplikasi | 8.880 | 10.246 | 18.235 |
| Performa Aplikasi | 3.272 | 3.350 | 6.613 |
| Keamanan Aplikasi | 2.449 | 1.793 | 3.697 |
| Desain Antarmuka | 347 | 929 | 3.014 |
| Permintaan Fitur | 462 | 694 | 2.393 |
| Total | 15.410 | 17.012 | 33.952 |

- Versi 1 adalah Save Version notebook `00-scraping-dataset-app-feedback` (151.850 ulasan mentah).
- Kolom tengah adalah aturan revisi pada `scraped_reviews_all.csv` versi 1 (102.708 ulasan setelah filter awal). Kolom kanan menambah 93.499 ulasan relevan unik hasil scraping 27 Nov 2026 di luar Kaggle (total 245.349 mentah, 195.110 setelah filter awal). Angka resmi menunggu Save Version berikutnya; jumlah ulasan relevan dapat sedikit berbeda karena Google Play terus berubah.
- Desain Antarmuka dan Permintaan Fitur memang jarang di sumber data: hanya sekitar 390 ulasan mentah yang memuat kata "tampil" dan sekitar 2.600 yang memuat "fitur". Ketidakseimbangan yang tersisa ditangani di notebook 03.

## Berkas dan skema

| Berkas | Dihasilkan oleh | Kolom | Keterangan |
|---|---|---|---|
| `scraped_reviews_all.csv` | Notebook 00 | `review_id, app, app_id, rating, date, text, thumbs_up` | Ulasan mentah urutan terbaru sebelum filter. Ikut di Output setiap versi, sehingga versi berikutnya dapat memakainya tanpa scraping ulang. |
| `scraped_reviews_relevant.csv` | Notebook 00 | sama dengan di atas | Ulasan mentah urutan paling relevan, diperlakukan sama. |
| `raw_dataset.csv` | Notebook 00 | `text, label, app, rating, date, source` | Dataset berlabel, teks masih asli. |
| `clean_dataset_part1.csv` | Notebook 01 (rencana) | `text, clean_text, label` (ditambah kolom bantu) | Hasil preprocessing. |
| `link_dataset.txt` | Notebook 00 | Teks | Daftar URL sumber data. |

Kolom wajib tugas adalah `text` dan `label`. Kolom lain dipakai untuk EDA.

## Penyimpanan

- CSV tidak di-commit ke repositori (lihat `.gitignore`). Tempat resminya adalah Kaggle Dataset dan folder pengumpulan.
- Tautan Kaggle Dataset dicatat di [`data/README.md`](../data/README.md) setelah dibuat.

## Etika dan privasi

- Hanya teks ulasan, rating, tanggal, nama aplikasi, dan jumlah suka yang disimpan. Nama pengguna dan foto profil tidak diambil.
- Data dipakai untuk keperluan akademik, bukan komersial.
- Ulasan dapat memuat nomor telepon atau data pribadi yang ditulis pengguna sendiri. Regex cleaning di notebook 01 menghapus angka panjang sehingga informasi tersebut tidak ikut ke `clean_text`.

## Keterbatasan

- Label dihasilkan dari aturan kata kunci, sehingga model cenderung mempelajari kata kunci yang sama. Pemeriksaan manual sampel per label wajib dilakukan dan hasilnya dilaporkan.
- Ulasan yang membahas lebih dari satu topik dibuang jika skornya berimbang, sehingga dataset lebih bersih tetapi kurang mewakili ulasan campuran.
- Library scraping tidak resmi dan dapat berubah perilakunya jika Google Play memperbarui situsnya.
