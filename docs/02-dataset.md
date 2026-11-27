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
- Ulasan diambil per bintang 1 sampai 5 dengan `filter_score_with`, urutan `Sort.NEWEST`, target `REVIEWS_PER_STAR = 8000` per aplikasi per bintang. Pengambilan per bintang menjaga keragaman topik: bintang rendah kaya keluhan bug, performa, dan keamanan, sedangkan bintang tinggi lebih sering memuat pujian tampilan dan usulan fitur.
- Google Play mengembalikan sekitar 200 ulasan per permintaan, sehingga notebook mengulang dengan `continuation_token` sampai target tercapai atau ulasan habis.
- Jeda `time.sleep(0.3)` antar permintaan agar tidak membebani server.
- Flag `SCRAPE`: `True` untuk scraping baru (butuh Internet di Kaggle), `False` untuk membaca `scraped_reviews_all.csv` dari Input Kaggle. Save Version selalu memakai `SCRAPE = False` agar hasil stabil.

## Filter awal

1. Buang ulasan kosong.
2. Rapikan spasi dan baris baru (isi teks tidak diubah).
3. Buang ulasan kurang dari 5 kata (`MIN_WORDS = 5`) karena terlalu pendek untuk menunjukkan topik.
4. Buang duplikat berdasarkan teks huruf kecil.

## Pelabelan

Pelabelan otomatis berbasis kata kunci berbobot, lalu diperiksa manual per label. Ulasan yang tidak relevan atau ambigu dibuang. Detail di [03-labeling-guidelines.md](03-labeling-guidelines.md).

## Kebijakan ukuran dan keseimbangan

- Semua ulasan yang lolos pelabelan dipakai. Tidak ada batas maksimal dan tidak ada downsampling paksa, supaya data tidak terbuang.
- `MAX_PER_LABEL = None` tersedia sebagai opsi jika suatu saat diperlukan pembatasan.
- Ketidakseimbangan kelas ditangani di tahap pemodelan (notebook 03) dengan `class_weight='balanced'` dan evaluasi per kelas (precision, recall, F1, macro average).

Uji awal di lingkungan pengembangan dengan sekitar 3.000 ulasan nyata menunjukkan sekitar 14 persen ulasan lolos pelabelan, dengan Bug Aplikasi sekitar 50 persen dan Desain Antarmuka paling sedikit (sekitar 7 persen). Angka final menunggu scraping penuh di Kaggle.

## Berkas dan skema

| Berkas | Dihasilkan oleh | Kolom | Keterangan |
|---|---|---|---|
| `scraped_reviews_all.csv` | Notebook 00, `SCRAPE = True` | `review_id, app, app_id, rating, date, text, thumbs_up` | Semua ulasan mentah sebelum filter. Diunggah sebagai Kaggle Dataset. |
| `raw_dataset.csv` | Notebook 00 | `text, label, app, rating, date` | Dataset berlabel, teks masih asli. |
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
