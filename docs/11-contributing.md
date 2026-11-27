# 11. Panduan Kontribusi

Aturan utama ada di [CLAUDE.md](../CLAUDE.md). Dokumen ini menjelaskan langkah praktisnya.

## Persiapan sekali

```bash
git clone https://github.com/zhafrandzaky/app-feedback-classifier.git
cd app-feedback-classifier
git config user.name "Nama Lengkap"
git config user.email "email-github-anda@example.com"
```

Gunakan email yang terdaftar di akun GitHub masing-masing, atau email noreply GitHub (`<id>+<username>@users.noreply.github.com`), agar commit tercatat atas nama anggota yang benar.

## Alur kerja

```bash
git switch main
git pull
git switch -c feat/eda-preprocessing
# ... kerjakan perubahan ...
git add notebooks/01_eda_preprocessing.ipynb docs/04-preprocessing.md
git commit -m "feat(eda): add eda and preprocessing notebook"
git push -u origin feat/eda-preprocessing
```

Lalu buka pull request ke `main`. Minimal satu anggota lain meninjau sebelum digabung.

## Pesan commit

- Format Conventional Commits, Bahasa Inggris, bentuk perintah, huruf kecil.
- Tanpa emoji.
- Tanpa trailer `Co-Authored-By`, `Generated with`, atau penanda AI lain. Periksa kembali pesan commit jika memakai asisten AI.

## Checklist sebelum pull request

- [ ] Smoke test (Run All) dan Save Version berhasil di Kaggle tanpa mengubah kode.
- [ ] Tidak ada file data besar, model, `kaggle.json`, atau `.env` yang ikut ter-commit.
- [ ] Dokumentasi di `docs/` diperbarui jika pipeline, label, atau dataset berubah.
- [ ] Tidak ada emoji di kode, dokumen, atau pesan commit.
- [ ] Label ditulis persis sesuai lima label resmi.

## Meninjau pull request

- Periksa kebenaran logika dan kejelasan penjelasan markdown.
- Pastikan perubahan dapat dijelaskan oleh setiap anggota saat tanya jawab.
- Gunakan fitur suggestion GitHub untuk perbaikan kecil.
