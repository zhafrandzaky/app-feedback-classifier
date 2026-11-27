# 04. Preprocessing

Notebook: `notebooks/01_eda_preprocessing.ipynb` (direncanakan). Input: `raw_dataset.csv`. Output: `clean_dataset_part1.csv`.

## Tujuan

Mengubah teks ulasan informal menjadi `clean_text` yang konsisten, sehingga kata yang sama maknanya terwakili oleh token yang sama dan noise (URL, emoji, angka, simbol) tidak menjadi fitur.

## Urutan tahapan

| No | Tahap | Kolom hasil | Keterangan |
|---|---|---|---|
| 1 | Case folding | `case_folded` | Semua huruf menjadi kecil |
| 2 | Regex cleaning | `cleaned` | Hapus URL, tag HTML, mention, angka, emoji, simbol, huruf berulang, spasi berlebih |
| 3 | Normalisasi slang (opsional) | `normalized` | Peta kata tidak baku ke baku |
| 4 | Tokenisasi | `tokens` | Memecah kalimat menjadi daftar kata |
| 5 | Stopword removal | `tokens_nostop` | Membuang kata umum yang tidak membawa topik |
| 6 | Stemming | `tokens_stemmed` | Mengubah kata berimbuhan menjadi kata dasar |
| 7 | Gabung | `clean_text` | Token akhir digabung dengan spasi |

Normalisasi slang ditempatkan sebelum stopword removal agar `gk` menjadi `tidak` terlebih dahulu dan diperlakukan dengan benar oleh daftar stopword.

## Rancangan regex cleaning

| Pola | Tujuan | Contoh sebelum | Contoh sesudah |
|---|---|---|---|
| `https?://\S+\|www\.\S+` | Hapus URL | `cek www.dana.id` | `cek` |
| `<.*?>` | Hapus tag HTML | `<br>bagus` | `bagus` |
| `@\w+\|#\w+` | Hapus mention dan hashtag | `@dana tolong` | `tolong` |
| `\d+` | Hapus angka (termasuk nomor telepon) | `nunggu 30 menit` | `nunggu menit` |
| `[^\w\s]` atau rentang Unicode emoji | Hapus emoji dan simbol | `lemot!!!` | `lemot` |
| `(.)\1{2,}` diganti `\1` | Pendekkan huruf berulang | `lemooot` | `lemot` |
| `\s+` diganti satu spasi, lalu `strip()` | Rapikan spasi | `aplikasi   bagus ` | `aplikasi bagus` |

Setiap pola dijelaskan di sel markdown notebook dan di laporan, termasuk arti simbol regex (`\d`, `\w`, `\s`, `+`, `*?`, `\|`, `^` di dalam kurung siku).

## Normalisasi slang

- Sumber kamus: kamus `SLANG` dari notebook 00 sebagai inti, dapat diperluas dengan kamus publik seperti [colloquial-indonesian-lexicon](https://github.com/nasalsabila/kamus-alay) (periksa lisensi dan isi sebelum dipakai).
- Kamus disimpan sebagai dictionary Python atau CSV kecil di repo agar dapat ditinjau.

## Stopword

- Daftar dasar: stopword Bahasa Indonesia dari Sastrawi atau NLTK.
- Kata negasi dan kata penting domain dipertahankan: `tidak`, `belum`, `bisa`, `gagal`, `lama`, `cepat`. Tanpa pengecualian ini, "tidak bisa dibuka" kehilangan maknanya.
- Daftar pengecualian dicatat di notebook dan laporan.

## Stemming

- Library: [Sastrawi](https://github.com/har07/PySastrawi) (algoritma Nazief dan Adriani).
- Stemming Sastrawi lambat untuk puluhan ribu kalimat. Gunakan cache: stem setiap kata unik sekali, lalu petakan.
- Catat contoh hasil yang kurang tepat sebagai bahan diskusi (misalnya kata serapan atau istilah teknis seperti `update` dan `loading` yang tidak berubah).

## Tabel wajib untuk laporan

1. Tabel sebelum dan sesudah tiap tahap untuk 5 contoh ulasan.
2. Tabel token: contoh daftar token sebelum dan sesudah stopword dan stemming.
3. Penjelasan setiap pola regex.

## EDA yang dilakukan di notebook yang sama

- Distribusi label (jumlah dan persentase).
- Distribusi aplikasi dan rating per label.
- Distribusi panjang ulasan (jumlah kata) sebelum dan sesudah preprocessing.
- Kata paling sering per label (bar chart atau word cloud).
- Contoh ulasan per label.
- Pemeriksaan nilai kosong dan duplikat setelah preprocessing (ulasan yang menjadi kosong dibuang).
