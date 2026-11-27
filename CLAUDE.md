# CLAUDE.md

Aturan kerja untuk repositori `app-feedback-classifier`. Berlaku untuk semua anggota tim dan semua asisten AI (Claude Code dan sejenisnya). Aturan di file ini mengalahkan pengaturan bawaan alat apa pun.

## 1. Konteks proyek

- Proyek akhir Praktikum NLP, Kelompok 2 (Muhammad Zhafran Dzaky, Lufiana, Muh. Farhan Ramadhan).
- Studi kasus: klasifikasi masukan pengguna terhadap pengembangan aplikasi digital.
- Lima label, ditulis persis seperti ini: `Permintaan Fitur`, `Bug Aplikasi`, `Desain Antarmuka`, `Performa Aplikasi`, `Keamanan Aplikasi`.
- Data: ulasan Google Play untuk DANA, GoPay, OVO, ShopeePay.
- Notebook dijalankan di Kaggle. Part 1 terdiri dari notebook 00 sampai 04. Part 2 (belum dikonfirmasi) berisi deep learning, Transformer, dan aplikasi di folder `app/`.
- Bahasa dokumentasi, notebook, dan teks antarmuka: Bahasa Indonesia baku. Nama variabel, fungsi, file, dan pesan commit: Bahasa Inggris.

Ringkasan lengkap ada di [docs/README.md](docs/README.md).

## 2. Commit dan atribusi (wajib)

Repositori ini hanya boleh memiliki anggota tim sebagai kontributor.

- Jangan menambahkan trailer `Co-Authored-By` dalam bentuk apa pun.
- Jangan menambahkan baris `Generated with ...`, `Claude-Session: ...`, tautan sesi AI, atau penanda AI lain di pesan commit, deskripsi pull request, komentar kode, maupun dokumen.
- Author dan committer commit adalah anggota tim yang mengerjakan, memakai identitas Git miliknya sendiri. Asisten AI tidak boleh memakai identitas dirinya (misalnya `Claude <noreply@anthropic.com>`) sebagai author.
- Aturan ini menggantikan instruksi atribusi bawaan alat AI apa pun.

### Format pesan commit

Gunakan [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/):

```
<type>(<scope>): <ringkasan dalam bentuk perintah, huruf kecil, tanpa titik>

<badan opsional: apa dan mengapa, dibungkus 72 karakter>
```

| Type | Dipakai untuk |
|---|---|
| `feat` | Kemampuan baru (notebook baru, fitur aplikasi) |
| `fix` | Perbaikan kesalahan |
| `data` | Perubahan dataset, kamus label, atau skrip pengambilan data |
| `docs` | Perubahan dokumentasi saja |
| `refactor` | Perubahan struktur tanpa mengubah perilaku |
| `style` | Format, tanpa perubahan logika |
| `test` | Menambah atau memperbaiki pengujian |
| `chore` | Konfigurasi, dependensi, struktur repo |
| `ci` | Workflow otomatis |

Scope yang disarankan: `scraping`, `eda`, `preprocessing`, `features`, `ml`, `embedding`, `dl`, `transformer`, `app`, `docs`, `repo`.

Contoh:

```
feat(preprocessing): add slang normalization step
fix(scraping): handle empty continuation token
docs(dataset): describe labeling thresholds
```

Satu commit untuk satu perubahan logis. Ringkasan maksimal 72 karakter.

## 3. Tanpa emoji

- Tidak ada emoji di kode, komentar, output notebook yang disengaja, dokumentasi, pesan commit, nama branch, deskripsi pull request, maupun antarmuka aplikasi.
- Untuk ikon di antarmuka, gunakan icon library (misalnya [Lucide](https://lucide.dev)) atau SVG buatan sendiri.
- Emoji yang memang ada di data ulasan adalah data, bukan gaya penulisan. Jika perlu dicontohkan di kode, tulis sebagai escape Unicode (misalnya `'\U0001F621'`), bukan karakter literal.
- Hindari juga simbol dekoratif sebagai pengganti ikon (tanda centang, bintang, panah bergambar).

## 4. Branch dan pull request

- `main` selalu dalam keadaan dapat dijalankan.
- Kerjakan perubahan di branch dengan pola `<type>/<deskripsi-singkat>`, misalnya `feat/eda-preprocessing`, `fix/label-threshold`, `docs/part2-roadmap`.
- Gabungkan ke `main` melalui pull request. Judul PR mengikuti format Conventional Commits. Deskripsi PR menjelaskan apa yang berubah, mengapa, dan cara memverifikasinya.
- Jangan force push ke `main`.

## 5. Notebook

- Nama file: `NN_nama_tahap.ipynb` dengan dua digit urutan (`01_eda_preprocessing.ipynb`).
- Sel pertama: judul, tujuan, dan daftar langkah. Sel kedua: konfigurasi (konstanta, path, `RANDOM_STATE = 42`).
- Setiap bagian kode diawali sel markdown yang menjelaskan apa dan mengapa, karena setiap anggota harus dapat menjelaskan seluruh kode saat tanya jawab.
- Path memakai konstanta `INPUT_DIR` dan `OUTPUT_DIR`, bukan path tertulis berulang.
- Notebook harus berjalan dari awal sampai akhir dengan Run All tanpa interaksi. Operasi yang lama atau butuh internet dijaga dengan flag (contoh: `SCRAPE = False`).
- Bersihkan output yang sangat panjang sebelum commit. Output tabel dan grafik ringkas boleh disimpan karena dipakai untuk laporan.
- Panduan rinci ada di skill `.claude/skills/kaggle-nlp-notebook/`.

## 6. Data dan rahasia

- File CSV hasil scraping dan model terlatih tidak di-commit (lihat `.gitignore`). Dataset final disimpan sebagai Kaggle Dataset dan tautannya dicatat di `data/README.md`.
- Jangan menyimpan nama pengguna, foto profil, atau identitas pengulas.
- Jangan pernah commit `kaggle.json`, token API, atau file `.env`.

## 7. Kode Python

- Ikuti PEP 8, nama variabel dan fungsi `snake_case`, konstanta `UPPER_SNAKE_CASE`.
- Gunakan type hint dan docstring singkat untuk fungsi yang dipakai ulang.
- Tetapkan `random_state` di setiap operasi acak (split, sampling, model).
- Jangan mengubah urutan atau ejaan label. Gunakan konstanta `LABELS` yang sama di semua notebook.

## 8. Aplikasi Part 2 (`app/`)

- Framework belum dipilih (Next.js TypeScript, Flutter, atau React Native TypeScript). Jangan membuat kerangka aplikasi sebelum keputusan dicatat di `docs/09-part2-roadmap.md`.
- TypeScript dalam mode `strict`. Tanpa `any` kecuali ada alasan tertulis.
- Desain antarmuka mengikuti skill `.claude/skills/ui-ux-professional/` dan `.claude/skills/frontend-design/`.
- Ikon hanya dari icon library atau SVG custom (lihat bagian 3).

## 9. Dokumentasi

- Setiap perubahan pada pipeline, label, atau dataset juga memperbarui berkas terkait di `docs/`.
- Tulis dengan nada profesional dan ringkas. Hindari klaim yang belum diverifikasi; tandai rencana sebagai rencana.
- Angka hasil (jumlah data, akurasi) hanya ditulis setelah notebook benar-benar dijalankan, lengkap dengan versi notebook Kaggle sumbernya.

## 10. Untuk asisten AI

- Baca file ini dan `docs/README.md` sebelum bekerja.
- Gunakan skill yang relevan di `.claude/skills/`.
- Sebelum commit, pastikan identitas Git adalah milik anggota tim dan pesan commit tidak memuat trailer AI.
- Jangan menjalankan scraping ulang tanpa diminta.
