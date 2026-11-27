# 03. Pedoman Pelabelan

Dokumen ini menjadi acuan tunggal arti setiap label, baik untuk aturan otomatis di notebook 00, pemeriksaan manual, maupun penjelasan saat presentasi.

## Prinsip umum

- Satu ulasan, satu label: topik utama yang paling ingin disampaikan pengguna kepada pengembang.
- Label menggambarkan aspek aplikasi, bukan sentimen. Ulasan positif tentang tampilan tetap Desain Antarmuka.
- Ulasan yang tidak membahas aplikasi (layanan pelanggan, promo, cashback, biaya admin, kurir, kebijakan perusahaan) tidak dilabeli dan dibuang.
- Ulasan yang dua topiknya sama kuat dianggap ambigu dan dibuang.

## Definisi label

### Permintaan Fitur

Pengguna mengusulkan sesuatu yang belum ada: fitur, menu, opsi, metode pembayaran, integrasi.

- Penanda: "tolong ditambahkan", "semoga ada fitur", "saran", "kenapa tidak ada opsi", "fitur ... dong".
- Contoh ilustrasi: "Saran saja, tolong ditambahkan fitur split bill biar gampang bayar bareng teman."
- Bukan label ini: "Fitur transfer error terus" (fiturnya ada tetapi rusak, maka Bug Aplikasi).
- Bukan label ini: permintaan non-fitur seperti menambah saldo, limit, nominal, cashback, atau gratis biaya admin; ketersediaan di kota tertentu; "saran saya jangan pakai aplikasi ini".

### Bug Aplikasi

Fungsi yang seharusnya berjalan ternyata tidak berjalan: error, crash, keluar sendiri, gagal proses, saldo terpotong tetapi transaksi gagal, tidak bisa dibuka setelah update.

- Penanda: "error", "bug", "force close", "keluar sendiri", "tidak bisa dibuka", "gagal transaksi", "setelah update".
- Contoh ilustrasi: "Setelah update kemarin aplikasinya keluar sendiri tiap buka menu riwayat."
- Bukan label ini: "Buka aplikasinya lama sekali" (berjalan tetapi lambat, maka Performa Aplikasi).
- Saldo atau transfer yang lama masuk dan transaksi tertunda (pending) diberi bobot ke label ini, bukan Performa Aplikasi.

### Desain Antarmuka

Tampilan dan pengalaman penggunaan: tata letak, warna, ukuran huruf, ikon, tombol, navigasi, kemudahan dipahami, mode gelap.

- Penanda: "tampilan", "desain", "UI", "tata letak", "tombol", "warna", "mode gelap", "membingungkan", "user friendly", "mudah digunakan", "susah dicari", "terlalu banyak iklan".
- Pujian kemudahan yang umum ("mudah digunakan", "praktis dipakai") tanpa menyebut tampilan, menu, atau navigasi terlalu umum dan tidak dilabeli. "Mudah digunakan, tampilannya simpel" tetap Desain Antarmuka.
- Contoh ilustrasi: "Tampilan baru membingungkan, menu riwayat susah dicari."
- Bukan label ini: "Tombol bayar tidak bisa ditekan" (fungsi rusak, maka Bug Aplikasi).

### Performa Aplikasi

Kecepatan dan efisiensi aplikasi saat berjalan: lambat, lemot, lag, hang, loading lama, boros baterai atau memori, ukuran aplikasi besar, server sibuk.

- Penanda: "lemot", "lambat", "lag", "hang", "loading lama", "berat", "boros baterai", "server down".
- Contoh ilustrasi: "Aplikasinya berat dan lemot, loading saldo saja bisa satu menit."
- Bukan label ini: "Loading terus lalu muncul terjadi kesalahan" (berakhir dengan error, cenderung Bug Aplikasi; diputuskan oleh skor).
- Bukan label ini: proses layanan yang lambat (penanganan laporan, refund, verifikasi, respon CS), karena itu layanan, bukan kinerja aplikasi.
- Negasi diperhitungkan: "tidak lemot" atau "bebas iklan, bug, ataupun lelet" tidak dihitung sebagai keluhan performa.

### Keamanan Aplikasi

Keamanan akun dan data pengguna: akun diretas atau dibobol, saldo hilang tanpa transaksi, penipuan, OTP, PIN, verifikasi wajah atau KTP, privasi data.

- Penanda: "keamanan", "diretas", "dibobol", "saldo hilang", "penipuan", "OTP", "PIN", "verifikasi", "data pribadi".
- Contoh ilustrasi: "Saldo saya hilang padahal tidak pernah transaksi, akun sepertinya dibobol."
- Bukan label ini: "OTP tidak masuk-masuk" (pengiriman gagal, aturan memberi bobot ke Bug Aplikasi).

Contoh di atas adalah ilustrasi buatan untuk menjelaskan definisi, bukan kutipan dataset.

## Aturan otomatis di notebook 00

### Normalisasi khusus pelabelan

Sebelum pencocokan kata kunci, teks dinormalisasi sementara (termasuk penandaan negasi: kata keluhan setelah `tidak`, `jangan`, `minim`, atau dalam daftar setelah `bebas`, `tanpa`, `anti` diberi awalan `neg_`): huruf kecil, hapus URL, huruf berulang dipendekkan (`lemooot` menjadi `lemoot`), simbol dan emoji diganti spasi, awalan `di` yang terpisah disambung untuk kata kerja umum (`di tambahkan` menjadi `ditambahkan`), akhiran `-nya` dilepas dari kata minimal 6 huruf (`fiturnya` menjadi `fitur`), slang dipetakan ke kata baku melalui kamus `SLANG` (`gk` menjadi `tidak`, `apk` menjadi `aplikasi`). Hasil normalisasi hanya dipakai untuk pencocokan. Kolom `text` tetap teks asli.

### Kata kunci berbobot

Setiap label memiliki daftar pola regex dengan bobot:

| Bobot | Arti | Contoh |
|---|---|---|
| 3 | Sangat khas label | `lemot` untuk Performa, `diretas` untuk Keamanan, `fitur baru` untuk Permintaan Fitur |
| 2 | Cukup khas | `loading`, `pin` atau `otp`, `warna` atau `tombol` |
| 1 | Petunjuk lemah, hanya membantu jika ada petunjuk lain | `fitur`, `cepat`, `aman` |
| 4-5 | Penanda yang hampir pasti | `tambahkan fitur`, `dark mode`, `dibobol` |

### Penentuan label

1. Skor tiap label adalah jumlah bobot pola yang cocok.
2. Label pemenang adalah skor tertinggi.
3. Ulasan dibuang jika skor tertinggi kurang dari `MIN_SCORE = 3` (tidak cukup bukti).
4. Ulasan dibuang jika selisih skor tertinggi dan kedua kurang dari `MIN_MARGIN = 2` (ambigu).
5. Label bantu `Lainnya` ikut bersaing skor tetapi tidak pernah masuk dataset. Polanya menandai topik di luar aplikasi: biaya admin, promo dan koin, paylater atau pinjaman, status dan upgrade akun, akun diblokir, layanan CS dan proses layanan yang lambat, permintaan saldo atau limit, ketersediaan per kota, paket data, ajakan ke pengguna lain ("saran saya jangan pakai"), komentar soal bintang, dan teks berbahasa Inggris. Ulasan yang dimenangkan `Lainnya` atau terlalu dekat skornya ikut dibuang.

## Pemeriksaan manual

Bagian 8 notebook 00 menampilkan 10 ulasan acak per label. Prosedur:

1. Setiap anggota memeriksa minimal satu label dan mencatat jumlah label yang benar dari sampel.
2. Pola salah label yang berulang diperbaiki di kamus `KEYWORDS` (tambah pola, kurangi pola, atau ubah bobot), lalu bagian 6 dijalankan ulang.
3. Ganti `random_state` untuk melihat sampel lain.
4. Catat perkiraan ketepatan label per kelas di laporan sebagai bagian dari keterbatasan.

## Riwayat perubahan aturan

| Tanggal | Perubahan | Alasan |
|---|---|---|
| 27 Nov 2026 | Versi awal kamus `KEYWORDS`, `MIN_SCORE = 3`, `MIN_MARGIN = 2` | Uji awal pada sekitar 3.000 ulasan nyata |
| 27 Nov 2026 | Revisi 1: pola Permintaan Fitur lebih ketat (permintaan harus menyebut fitur, menu, opsi, atau kata kerja "ditambahkan"; "semoga bisa" saja tidak cukup), pola Desain Antarmuka diperluas (kemudahan pemakaian, iklan yang mengganggu, ukuran tulisan, tema), label bantu `Lainnya`, normalisasi awalan `di` dan akhiran `-nya`, `MIN_WORDS` 5 menjadi 3 | Save Version 1 di Kaggle: Desain 347 dan Permintaan Fitur 462 dari 15.410, serta banyak salah label pada Permintaan Fitur. Simulasi pada data yang sama: Desain 929, Permintaan Fitur 694 dari 17.012 |
| 27 Nov 2026 | Tambah sumber ulasan `Sort.MOST_RELEVANT` (8.000 per bintang per aplikasi), digabung dengan ulasan terbaru | Permintaan Fitur dan Desain Antarmuka jarang di ulasan terbaru. Simulasi: Desain 3.014 dan Permintaan Fitur 2.393 dari 33.952 |
| 27 Nov 2026 | Revisi 2: negasi ditandai `neg_`, pujian kemudahan umum menjadi bobot 1, kata `tampilan` saja bobot 2, bobot `saran` turun ke 1, pola `Lainnya` baru (saldo/limit/biaya admin, kota, blokir, proses layanan lambat, ajakan jangan pakai, komentar bintang), saldo lama masuk dan pending ke Bug, buang spam template | Save Version 2: cek 50 sampel per label menemukan banyak salah label. Perkiraan ketepatan dari cek sampel naik: Permintaan Fitur sekitar 34/50 menjadi 43/50, Desain sekitar 15/50 menjadi 41/50 |

Tambahkan baris baru setiap kali kamus atau ambang diubah.
