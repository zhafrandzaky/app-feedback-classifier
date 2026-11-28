# 04. Preprocessing

Notebook: `notebooks/01_eda_preprocessing.ipynb` (nama Kaggle `01-eda-preprocessing-app-feedback`). Input: `raw_dataset.csv` dari dataset `app-feedback-ewallet-reviews`. Output: `clean_dataset_part1.csv`.

## Tujuan

Mengubah teks ulasan informal menjadi `clean_text` yang konsisten, sehingga kata yang sama maknanya terwakili oleh token yang sama dan noise (URL, emoji, angka, simbol) tidak menjadi fitur.

## Sebelum preprocessing: buang ulasan hampir kembar

Notebook 00 sudah membuang duplikat persis. Notebook 01 juga membuang ulasan yang hanya berbeda huruf besar, tanda baca, spasi, atau emoji (misalnya `Loading nya lama banget` dan `Loading nya lama banget!!`). Kunci pembandingnya adalah teks huruf kecil yang hanya menyisakan huruf dan angka (`[^a-z0-9]+` diganti spasi). Hanya kemunculan pertama yang dipertahankan. Tujuannya mencegah salinan yang sama masuk ke data latih dan data uji sekaligus.

## Urutan tahapan

| No | Tahap | Kolom hasil | Keterangan |
|---|---|---|---|
| 1 | Case folding | `case_folded` | Semua huruf menjadi kecil |
| 2 | Regex cleaning | `cleaned` | Hapus URL, tag HTML, mention, emoji, angka, simbol, huruf berulang, spasi berlebih |
| 3 | Normalisasi slang | `normalized` | Peta kata tidak baku ke baku dengan kamus `SLANG` |
| 4 | Tokenisasi | `tokens` | Memecah kalimat menjadi daftar kata dengan `str.split()` |
| 5 | Stopword removal | `tokens_nostop` | Membuang kata umum dan token satu huruf, negasi dipertahankan |
| 6 | Stemming | `tokens_stemmed` | Mengubah kata berimbuhan menjadi kata dasar dengan Sastrawi |
| 7 | Gabung | `clean_text` | Token akhir digabung dengan spasi, baris kosong dibuang |

Normalisasi slang ditempatkan sebelum stopword removal agar `gk` menjadi `tidak` terlebih dahulu dan diperlakukan dengan benar oleh daftar stopword.

## Regex cleaning

Pola dijalankan berurutan. Bagian yang dibuang diganti spasi agar dua kata tidak tersambung.

| No | Pola | Pengganti | Tujuan | Contoh sebelum | Contoh sesudah |
|---|---|---|---|---|---|
| 1 | `https?://\S+\|www\.\S+` | spasi | Hapus URL | `cek www.dana.id` | `cek` |
| 2 | `<[^>]+>\|&\w+;` | spasi | Hapus tag dan entitas HTML | `bagus<br>tapi &amp; berat` | `bagus tapi berat` |
| 3 | `[@#]\w+` | spasi | Hapus mention dan hashtag | `@gopay tolong` | `tolong` |
| 4 | `EMOJI_PATTERN` (rentang Unicode emoji, ditulis sebagai escape) | spasi | Hapus emoji | `kecewa\U0001F621` | `kecewa` |
| 5 | `\b([a-z]{2,})2\b` | `\1` | Kata ulang dengan angka 2 menjadi kata dasar | `fitur2 baru` | `fitur baru` |
| 6 | `\d+` | spasi | Hapus angka (nominal, nomor telepon, tanggal) | `nunggu 30 menit` | `nunggu menit` |
| 7 | `[^a-z\s]` | spasi | Hapus tanda baca, simbol, dan sisa karakter non-Latin | `error!!! kenapa??` | `error kenapa` |
| 8 | `([a-z])\1{2,}` | `\1` | Pendekkan huruf yang berulang tiga kali atau lebih | `lemooot` | `lemot` |
| 9 | `\s+` lalu `strip()` | satu spasi | Rapikan spasi | `aplikasi   bagus ` | `aplikasi bagus` |

Notebook menjelaskan arti setiap simbol regex (`\d`, `\w`, `\s`, `+`, `*?`, `{2,}`, `?`, `\|`, `[^...]`, `\1`, `\b`) dan menguji setiap pola secara terpisah pada contoh kalimat.

Perbedaan dengan notebook 00: di notebook 00 huruf berulang dipendekkan menjadi dua huruf (`\1\1`) karena hanya untuk mencocokkan kata kunci pelabelan. Di notebook 01 dipendekkan menjadi satu huruf (`\1`) agar varian ejaan menjadi satu token. Kata baku berhuruf ganda (`saat`) tidak berubah karena pola hanya aktif untuk tiga huruf atau lebih.

## Normalisasi slang

- Kamus `SLANG` berupa dictionary Python di notebook, sehingga dapat ditinjau langsung.
- Inti kamus diambil dari kamus `SLANG` notebook 00 agar pelabelan dan preprocessing konsisten, lalu ditambah kata tidak baku yang sering muncul di dataset ini (misalnya `udh`, `skrg`, `tetep`, `nunggu`, `ilang`, `betulin`).
- Pemetaan notebook 00 yang khusus untuk pelabelan dan berisiko mengubah makna tidak dipakai. Contohnya `kasih` menjadi `beri` tidak dipakai karena akan mengubah `terima kasih`.
- Kamus publik seperti [colloquial-indonesian-lexicon](https://github.com/nasalsabila/kamus-alay) tidak dipakai agar notebook tidak bergantung pada unduhan saat dijalankan dan setiap pemetaan dapat dijelaskan.

## Stopword

- Daftar dasar: stopword Bahasa Indonesia dari Sastrawi.
- Tambahan partikel informal yang tidak ada di daftar Sastrawi: `nya`, `sih`, `dong`, `deh`, `kok`, `lah`, `kan`, `nih`, `tuh`, `loh`, `yah`, `kak`, `min`, dan sejenisnya.
- Pengecualian (`KEEP_WORDS`), tetap dipertahankan walaupun ada di daftar Sastrawi:

| Kata | Alasan |
|---|---|
| `tidak`, `belum`, `bukan`, `jangan`, `kurang` | Negasi. Tanpa kata ini "tidak bisa dibuka" kehilangan maknanya |
| `bisa`, `dapat` | Bagian dari keluhan "tidak bisa", "tidak dapat" |
| `tolong`, `harus`, `ingin`, `seharusnya` | Penanda permintaan fitur |
| `lama`, `cepat`, `lambat`, `gagal`, `baru` | Penanda performa, bug, dan pembaruan |

- Token satu huruf (sisa singkatan seperti `d`, `x`) ikut dibuang.

## Stemming

- Library: [Sastrawi](https://github.com/har07/PySastrawi) (algoritma Nazief dan Adriani).
- Setiap kata unik di-stem sekali, lalu hasilnya disimpan di `stem_cache` dan dipetakan ke semua ulasan. Kata unik dibagi ke beberapa proses CPU (`multiprocessing`) karena Sastrawi memerlukan sekitar 10 sampai 60 milidetik per kata baru.
- `STEM_PROTECT`: kata yang tidak di-stem karena Sastrawi salah memotongnya atau karena istilah teknis, misalnya `lemot` (Sastrawi menghasilkan `lot`), `jaringan` (menjadi `jaring`), `error`, `login`, `otp`, `qris`.
- `STEM_OVERRIDE`: kata yang hasil stem-nya ditetapkan manual. `perbaiki`, `diperbaiki`, dan `perbaikan` oleh Sastrawi menjadi `baik`, sehingga permintaan perbaikan tercampur dengan pujian. Semuanya dipetakan ke `perbaiki`.
- Contoh hasil yang masih kurang tepat dicatat sebagai bahan diskusi, misalnya `pengguna` dan `menggunakan` sama-sama menjadi `guna`, dan `keamanan` menjadi `aman`.

## Output

| Berkas | Isi |
|---|---|
| `clean_dataset_part1.csv` | `text, clean_text, label, app, rating, source` |
| `raw_dataset.csv` | Salinan Input, agar Output lengkap untuk folder pengumpulan |
| `preprocessing_examples.csv` | Tabel before/after lima contoh ulasan |
| `preprocessing_stats.csv` | Jumlah kata unik dan rata-rata kata per ulasan di setiap tahap |
| `figures/*.png` | Grafik EDA, 200 dpi |

Kolom tahap antara (`cleaned`, `tokens`, dan seterusnya) tidak disimpan karena dapat dibuat ulang dari notebook. Ulasan yang `clean_text`-nya kosong dibuang. Ulasan dengan `clean_text` yang sama tetapi teks aslinya berbeda tetap dipertahankan (umumnya ulasan pendek seperti "aplikasi lemot").

## Tabel wajib untuk laporan

1. Tabel sebelum dan sesudah tiap tahap untuk 5 contoh ulasan (satu per label), dalam bentuk lebar dan vertikal.
2. Tabel token: daftar token setelah tokenisasi, setelah stopword removal, dan setelah stemming.
3. Penjelasan setiap pola regex (tabel di atas, juga di notebook bagian 6.2).
4. Statistik per tahap: jumlah kata unik dan rata-rata kata per ulasan.

## EDA yang dilakukan di notebook yang sama

- Pemeriksaan awal: tipe kolom, nilai kosong, duplikat, label di luar daftar.
- Distribusi label (jumlah dan persentase, rasio label terbanyak dan tersedikit).
- Komposisi label per aplikasi dan sebaran rating per label.
- Panjang ulasan per label (boxplot), serta sebelum dan sesudah preprocessing (histogram).
- Ciri noise pada teks mentah: huruf kapital, URL, angka, emoji, tanda baca berulang, huruf berulang, kata slang.
- Contoh ulasan per label.
- Kata terbanyak sebelum preprocessing, lalu kata terbanyak dan word cloud per label setelah preprocessing.

## Hasil

Angka resmi (jumlah ulasan akhir, kata unik per tahap) diisi setelah Save Version notebook `01-eda-preprocessing-app-feedback` di Kaggle, beserta nomor versinya.
