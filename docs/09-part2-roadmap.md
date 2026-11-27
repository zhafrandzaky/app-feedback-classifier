# 09. Rencana Part 2

Status: rencana, menunggu konfirmasi asisten praktikum. Isi dokumen ini diperbarui setelah ketentuan Part 2 diumumkan.

## Perkiraan cakupan

1. Model deep learning dan Transformer.
2. Aplikasi web atau mobile yang memanfaatkan model.

## Model

| Tahap | Kandidat | Catatan |
|---|---|---|
| Deep learning | LSTM atau BiLSTM, CNN teks | Embedding dari Word2Vec notebook 04 atau dilatih ulang |
| Transformer | IndoBERT (`indobenchmark/indobert-base-p1`), IndoBERTweet, atau DistilBERT multibahasa | Fine-tuning di Kaggle dengan GPU |
| Pembanding | Model klasik terbaik dari notebook 03 | Dipakai sebagai baseline |

Evaluasi memakai split yang sama dengan Part 1 agar perbandingan adil, dengan macro F1 sebagai metrik utama.

## Penyajian model

Rencana awal, akan disesuaikan dengan framework yang dipilih:

- API inferensi ringan (misalnya FastAPI) yang menerima teks dan mengembalikan label beserta probabilitas.
- Alternatif tanpa server: model klasik atau model Transformer yang dikonversi ke ONNX, atau layanan hosting model seperti Hugging Face Spaces.

## Aplikasi

Framework belum dipilih. Kandidat:

| Framework | Kelebihan | Pertimbangan |
|---|---|---|
| Next.js (TypeScript) | Web, mudah dideploy, ekosistem UI luas | Butuh API model terpisah |
| Flutter | Satu basis kode untuk Android dan iOS, performa baik | Bahasa Dart |
| React Native (TypeScript) | Satu bahasa dengan Next.js, ekosistem npm | Konfigurasi build mobile |

Fitur aplikasi yang direncanakan:

1. Input satu ulasan, tampilkan label, tingkat keyakinan, dan kata yang paling berpengaruh.
2. Unggah file CSV ulasan, tampilkan distribusi kategori.
3. Dashboard ringkas per aplikasi e-wallet.
4. Halaman penjelasan model dan dataset.

Prinsip desain:

- Mengikuti skill `.claude/skills/ui-ux-professional/` dan `.claude/skills/frontend-design/`.
- Ikon dari Lucide atau SVG custom, tanpa emoji.
- Teks antarmuka Bahasa Indonesia, responsif, aksesibel (kontras, fokus keyboard, label form).

## Keputusan yang perlu dicatat

| Keputusan | Pilihan | Tanggal | Alasan |
|---|---|---|---|
| Framework aplikasi | Belum | | |
| Model Transformer | Belum | | |
| Hosting model | Belum | | |

Struktur folder `app/` dibuat setelah framework diputuskan.
