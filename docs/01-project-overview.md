# 01. Gambaran Proyek

## Latar belakang

Aplikasi dompet digital (e-wallet) seperti DANA, GoPay, OVO, dan ShopeePay dipakai puluhan juta orang di Indonesia. Pengguna menyampaikan pengalaman mereka melalui ulasan di Google Play: keluhan error, aplikasi yang lambat, kekhawatiran keamanan akun, kritik tampilan, sampai usulan fitur baru.

Bagi tim pengembang, ulasan ini adalah masukan langsung yang bernilai, tetapi jumlahnya ribuan per minggu dan ditulis dalam bahasa informal yang penuh singkatan dan slang. Membaca dan memilah secara manual tidak efisien. Klasifikasi teks otomatis membantu mengarahkan setiap masukan ke tim yang tepat: tim QA untuk bug, tim infrastruktur untuk performa, tim desain untuk antarmuka, tim keamanan untuk laporan akun, dan tim produk untuk permintaan fitur.

## Rumusan masalah

1. Bagaimana membangun dataset ulasan berbahasa Indonesia yang berlabel kategori masukan pengembangan aplikasi?
2. Bagaimana tahapan preprocessing yang tepat untuk teks ulasan informal berbahasa Indonesia?
3. Seberapa baik representasi Bag of Words dan TF-IDF yang dipadukan dengan model machine learning klasik dalam mengklasifikasikan ulasan ke lima kategori?
4. Bagaimana word embedding (Word2Vec) menangkap hubungan makna antar kata dalam domain ulasan e-wallet?

## Tujuan

1. Mengumpulkan minimal 3.000 ulasan berlabel dari Google Play melalui scraping.
2. Menyusun pipeline preprocessing yang terdokumentasi dan dapat direproduksi.
3. Membandingkan empat model klasik (Naive Bayes, SVM, Logistic Regression, Random Forest) dengan metrik accuracy, precision, recall, dan F1-score.
4. Melatih model Word2Vec dan menganalisis kata-kata yang berdekatan dalam ruang vektor.
5. Menyiapkan fondasi untuk Part 2: model deep learning atau Transformer dan aplikasi yang dapat dipakai.

## Label

| Label | Definisi singkat |
|---|---|
| Permintaan Fitur | Pengguna mengusulkan fitur, menu, atau opsi yang belum ada |
| Bug Aplikasi | Fungsi tidak berjalan sebagaimana mestinya: error, crash, gagal proses |
| Desain Antarmuka | Komentar tentang tampilan, tata letak, navigasi, kemudahan penggunaan |
| Performa Aplikasi | Kecepatan dan efisiensi: lambat, lag, loading lama, boros sumber daya |
| Keamanan Aplikasi | Keamanan akun dan data: peretasan, penipuan, OTP, PIN, verifikasi, privasi |

Definisi lengkap dan kasus batas ada di [03-labeling-guidelines.md](03-labeling-guidelines.md).

## Ruang lingkup

Termasuk:

- Ulasan berbahasa Indonesia dari Google Play untuk empat aplikasi e-wallet.
- Klasifikasi multikelas dengan satu label per ulasan.

Tidak termasuk:

- Ulasan dari App Store atau media sosial.
- Analisis sentimen positif atau negatif (rating hanya dipakai untuk EDA).
- Ulasan yang membahas layanan pelanggan, promo, atau kebijakan bisnis tanpa kaitan dengan aplikasi. Ulasan seperti ini dibuang saat pelabelan.

## Alasan pemilihan domain e-wallet

- Volume ulasan tinggi dan aktif, sehingga mudah memenuhi syarat minimal 3.000 data.
- Kelima kategori muncul secara alami: e-wallet sering dikeluhkan soal bug transaksi, performa saat jam sibuk, dan keamanan akun.
- Tidak tumpang tindih dengan tema kelompok lain (tema perbankan sudah dipakai kelompok lain).
