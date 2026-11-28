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
| 4 | Koreksi typo | `corrected` | Kata langka yang tidak dikenal diganti kata sering yang paling mirip (jarak edit) |
| 5 | Tokenisasi | `tokens` | Memecah kalimat menjadi daftar kata dengan `str.split()` |
| 6 | Stopword removal | `tokens_nostop` | Membuang kata umum dan token satu huruf, negasi dipertahankan |
| 7 | Stemming | `tokens_stemmed` | Mengubah kata berimbuhan menjadi kata dasar dengan Sastrawi |
| 8 | Gabung | `clean_text` | Token akhir digabung dengan spasi, baris kosong dibuang |

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
- Inti kamus diambil dari kamus `SLANG` notebook 00 agar pelabelan dan preprocessing konsisten, lalu ditambah kata tidak baku yang sering muncul di dataset ini (misalnya `udh`, `skrg`, `tetep`, `nunggu`, `ilang`, `betulin`) dan kata tidak dikenal yang paling sering tersisa di `clean_text` (misalnya `gimna`, `krna`, `jngn`, `stiap`, `pdahal`). Audit output v4 menambahkan sekitar 125 bentuk lisan dan singkatan yang sering tersisa, misalnya `nyangkut` menjadi `tersangkut`, `ngasih` menjadi `kasih`, `nyoba` menjadi `coba`, `nungguin` menjadi `menunggu`, `sja` menjadi `saja`, dan `kmrn` menjadi `kemarin`. Audit output v5 menambahkan sekitar 65 entri lagi, terutama akhiran `-in` (`tingkatin`, `laporin`, `benahin`) dan singkatan (`tsb`, `sgt`, `lgsg`, `kdng`). Kamus kini berisi 503 entri. Setelah kamus diterapkan, istilah dua kata yang juga ditulis sebagai satu kata disatukan lewat `PHRASES` (`top up` menjadi `topup`, `log in` dan `sign in` menjadi `login`, `customer service` menjadi `cs`, `play store` menjadi `playstore`), sehingga tidak dihitung sebagai fitur berbeda.
- Pemetaan notebook 00 yang khusus untuk pelabelan dan berisiko mengubah makna tidak dipakai. Contohnya `kasih` menjadi `beri` tidak dipakai karena akan mengubah `terima kasih`.
- Kamus publik seperti [colloquial-indonesian-lexicon](https://github.com/nasalsabila/kamus-alay) tidak dipakai agar notebook tidak bergantung pada unduhan saat dijalankan dan setiap pemetaan dapat dijelaskan.

## Koreksi typo

Kamus slang hanya menangani singkatan yang sudah diketahui. Salah ketik yang tidak disengaja (`trnsaksi`, `jangn`, `updte`, `oengembalian`) ditangani dengan koreksi berbasis jarak edit, setelah normalisasi slang dan sebelum stopword removal.

- Jarak: *Optimal String Alignment* (OSA), yaitu jumlah minimum operasi sisip, hapus, ganti satu huruf, atau tukar dua huruf bersebelahan. Dihitung dengan library `rapidfuzz`.
- Kata `w` dikoreksi menjadi `k` hanya jika:
  1. `w` tidak dikenal: tidak ada di kamus kata dasar Sastrawi (sekitar 30.000 kata), bukan stopword, bukan istilah domain atau nama aplikasi (`DOMAIN_WORDS`), dan Sastrawi tidak menemukan kata dasarnya;
  2. `w` langka (paling banyak 10 kali), bukan kata di kamus `SLANG`, dan minimal 4 huruf;
  3. `k` sering (minimal 20 kali) dan minimal 10 kali lebih sering dari `w`, serta dikenal atau merupakan kata slang di kamus `SLANG` (frekuensi dihitung sebelum normalisasi slang, sehingga `gimna` dapat dikoreksi ke `gimana` lalu dibakukan menjadi `bagaimana`);
  4. jarak paling banyak 1, atau 2 untuk kata minimal 9 huruf;
  5. huruf pertama sama;
  6. hanya ada satu kandidat terbaik (jika ambigu, kata dibiarkan);
  7. untuk kata kurang dari 6 huruf, koreksi hanya boleh menambah huruf yang hilang (`jngn` menjadi `jangan`) atau membuang huruf ganda (`gakk` menjadi `gak`), karena kata pendek berbahasa Inggris (`hard`, `case`, `have`) mudah tertukar dengan kata Indonesia jika satu hurufnya diganti.
- `TYPO_MANUAL`: typo sering yang tidak tertangkap aturan (misalnya `oengembalian`, `ufgrate`, `msalah`), termasuk sekitar 60 typo yang muncul lebih dari 10 kali sehingga lolos syarat frekuensi (misalnya `tida`, `tdak`, `sring`, `applikasi`, `transper`). Daftar ini dipilih dari pemeriksaan output Kaggle v3: token langka yang berjarak satu huruf dari kata yang sangat sering. Audit output v4 menambah sekitar 45 pasangan lagi, misalnya `ttap`, `toup`, `apdate`, `shope`, `menggangu`, serta bentuk `di-` dari istilah teknis (`diupgrade`, `direfund`). Pasangan yang ambigu (`sma`, `back`, `date`, `mail`) sengaja tidak dimasukkan. `TYPO_IGNORE`: kata yang tidak boleh dikoreksi (nama tempat dan merek seperti `pertamina`, serta kasus yang terbukti salah). Termasuk di dalamnya bentuk lisan yang mirip bentuk lisan lain tetapi berbeda makna, misalnya `ngalahin` (bukan `ngalamin`) dan `batasin` (bukan `batalin`).
- Seluruh pasangan disimpan di `typo_corrections.csv` (typo, koreksi, sumber, jarak, frekuensi) untuk diperiksa dan dilampirkan di laporan.
- Keterbatasan: koreksi tidak sempurna untuk bahasa informal. Typo yang lolos direncanakan ditangani lagi oleh TF-IDF char n-gram (notebook 02) dan FastText (notebook 04).

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
- `melakukan`, `lakukan`, dan `dilakukan` dijadikan stopword. Dalam "melakukan transaksi" makna ada pada `transaksi`, dan jika di-stem kata ini menjadi `laku` yang berarti lain.

## Stemming

- Library: [Sastrawi](https://github.com/har07/PySastrawi) (algoritma Nazief dan Adriani).
- Setiap kata unik di-stem sekali, lalu hasilnya disimpan di `stem_cache` dan dipetakan ke semua ulasan.
- Sastrawi bawaan menyimpan kamus kata dasar sebagai list, sehingga setiap pengecekan menelusuri sekitar 30.000 kata dan satu kata bisa memerlukan puluhan milidetik. Notebook mengubah kamus itu menjadi set (`stemmer.delegatedStemmer.dictionary.words = set(...)`). Hasil stemming sama persis, tetapi waktu stemming seluruh data turun dari sekitar 854 detik (Save Version 1 di Kaggle) menjadi di bawah 1 detik pada uji lokal.
- `STEM_PROTECT`: kata yang tidak di-stem karena Sastrawi salah memotongnya, karena istilah teknis, atau karena maknanya berubah jika di-stem. Contoh: `lemot` (Sastrawi menghasilkan `lot`), `jaringan` (`jaring`), `menarik` (`tarik`, padahal di ulasan desain berarti bagus), `kejadian` (`jadi`), `alasan` (`alas`), `sebelah` (`belah`), `masukan` (saran, menjadi `masuk`), `pengguna` (`guna`), `sebelum` (`belum`, yang mengubah makna menjadi negasi), `mengerti` (`erti`), serta `error`, `login`, `otp`, `qris`. Perlindungan juga berlaku untuk bentuk berimbuhan sederhana (`lemotnya`, `selemot`, `terlemot`, `jaringannya`, `terupdate`), karena Sastrawi memotong `lemotnya` menjadi `lot`.
- `STEM_OVERRIDE`: kata yang hasil stem-nya ditetapkan manual. `perbaiki`, `diperbaiki`, dan `perbaikan` oleh Sastrawi menjadi `baik`, sehingga permintaan perbaikan tercampur dengan pujian; semuanya dipetakan ke `perbaiki`. Contoh lain: `berikan` (Sastrawi: `ikan`) menjadi `beri`, `memasukan` (`pasu`) menjadi `masuk`, `penarikan` (`tari`) menjadi `tarik`, `persetujuan` (`tuju`) menjadi `setuju`, `pemasukan` (`pasu`) menjadi `masuk`. Audit v5 menambahkan `bersihkan` (Sastrawi: `sih`) menjadi `bersih` serta `mengaku` dan `mengakui` (`aku`, kata ganti) menjadi `klaim`.
- Pelepasan `-nya` pada kata serapan: Sastrawi hanya melepas akhiran jika kata dasarnya ada di kamus, sehingga `fiturnya`, `servernya`, dan `notifnya` tidak berubah. Jika Sastrawi tidak mengubah kata berakhiran `-nya`, akhiran dilepas, sisanya dibakukan dengan `SLANG`, lalu di-stem ulang (`notifnya` menjadi `notifikasi`). Salah ketik satu `n` (`coinya`) dikembalikan ke bentuk yang lebih sering di data (`coin`).
- Contoh hasil yang masih kurang tepat dicatat sebagai bahan diskusi, misalnya `pengguna` dan `menggunakan` sama-sama menjadi `guna`, dan `keamanan` menjadi `aman`.

## Output

| Berkas | Isi |
|---|---|
| `clean_dataset_part1.csv` | `text, clean_text, label, app, rating, source` |
| `raw_dataset.csv` | Salinan Input, agar Output lengkap untuk folder pengumpulan |
| `preprocessing_examples.csv` | Tabel before/after lima contoh ulasan |
| `preprocessing_stats.csv` | Jumlah kata unik dan rata-rata kata per ulasan di setiap tahap |
| `typo_corrections.csv` | Pasangan typo dan koreksi beserta sumber, jarak, dan frekuensi |
| `figures/*.png` | Grafik EDA, 200 dpi |

Kolom tahap antara (`cleaned`, `corrected`, `tokens`, dan seterusnya) tidak disimpan karena dapat dibuat ulang dari notebook. Ulasan yang `clean_text`-nya kosong dibuang. Ulasan dengan `clean_text` yang sama tetapi teks aslinya berbeda tetap dipertahankan (umumnya ulasan pendek seperti "aplikasi lemot").

## Tabel wajib untuk laporan

1. Tabel sebelum dan sesudah tiap tahap untuk 5 contoh ulasan (satu per label), dalam bentuk lebar dan vertikal.
2. Tabel token: daftar token setelah tokenisasi, setelah stopword removal, dan setelah stemming.
3. Penjelasan setiap pola regex (tabel di atas, juga di notebook bagian 6.2).
4. Statistik per tahap: jumlah kata unik dan rata-rata kata per ulasan.
5. Tabel koreksi typo dan contoh ulasan sebelum dan sesudah koreksi.

## EDA yang dilakukan di notebook yang sama

- Pemeriksaan awal: tipe kolom, nilai kosong, duplikat, label di luar daftar.
- Distribusi label (jumlah dan persentase, rasio label terbanyak dan tersedikit).
- Komposisi label per aplikasi dan sebaran rating per label.
- Panjang ulasan per label (boxplot), serta sebelum dan sesudah preprocessing (histogram).
- Ciri noise pada teks mentah: huruf kapital, URL, angka, emoji, tanda baca berulang, huruf berulang, kata slang.
- Contoh ulasan per label.
- Kata terbanyak sebelum preprocessing, lalu kata terbanyak dan word cloud per label setelah preprocessing.

## Pemeriksaan kualitas `clean_text`

`clean_text` sengaja tidak terbaca seperti kalimat normal: stopword dibuang, imbuhan dilepas, dan angka serta tanda baca hilang. Yang diperiksa adalah apakah kata yang tersisa masih mewakili makna ulasan. Pemeriksaan sampel acak per label menemukan masalah sistematis yang sudah diperbaiki (stem yang mengubah makna, slang dan typo yang sering, akhiran `-nya` pada kata serapan). Sisa yang wajar: beberapa kata tidak baku yang jarang, nama merek atau game, dan sebagian kecil koreksi typo yang keliru (seluruhnya tercatat di `typo_corrections.csv`).

## Hasil

Angka resmi (jumlah ulasan akhir, kata unik per tahap) diisi setelah Save Version notebook `01-eda-preprocessing-app-feedback` di Kaggle, beserta nomor versinya.
