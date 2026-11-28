# 07. Word Embedding

Notebook: `notebooks/04_word_embedding.ipynb` (direncanakan).

## Metode

- Word2Vec dari `gensim`, dilatih pada token hasil preprocessing seluruh dataset.
- Arsitektur yang dibandingkan secara singkat: Skip-gram (`sg=1`) dan CBOW (`sg=0`). Skip-gram biasanya lebih baik untuk kata jarang pada korpus kecil.

## Parameter yang dicatat

| Parameter | Nilai awal | Arti |
|---|---|---|
| `vector_size` | 100 | Dimensi vektor kata |
| `window` | 5 | Jumlah kata konteks di kiri dan kanan |
| `min_count` | 2 | Kata yang muncul kurang dari ini diabaikan |
| `sg` | 1 | Skip-gram |
| `epochs` | 20 | Jumlah iterasi pelatihan |
| `seed` | 42 | Reprodusibilitas |
| `workers` | 1 | Satu worker agar hasil deterministik |

## Yang ditampilkan

1. Ukuran kosakata dan dimensi vektor.
2. Tabel 10 kata penting domain (misalnya `lemot`, `error`, `tampilan`, `fitur`, `otp`, `saldo`, `login`, `update`, `aman`, `loading`) dengan 5 kata paling mirip dan nilai cosine similarity.
3. Contoh potongan vektor satu kata.
4. Visualisasi 2D dengan PCA atau t-SNE untuk kata-kata tersebut, diwarnai berdasarkan label yang paling berkaitan.
5. Analisis: apakah kata dari label yang sama berkumpul, dan contoh hubungan yang kurang tepat.

## Perbandingan dengan FastText dan GloVe (untuk laporan)

| Metode | Kelebihan | Kekurangan |
|---|---|---|
| Word2Vec | Cepat, mudah dilatih pada korpus sendiri | Tidak dapat menangani kata di luar kosakata |
| FastText | Memakai subkata, tahan terhadap salah ketik dan slang | Model lebih besar |
| GloVe | Memakai statistik kemunculan global | Umumnya memakai model pralatih bahasa Inggris |

Rencana: notebook 04 melatih FastText (`gensim.models.FastText`) dengan parameter yang sama sebagai pembanding Word2Vec, termasuk contoh kata typo yang tetap mendapat vektor dekat dengan kata aslinya.

Untuk Part 2, rata-rata vektor kata dapat dicoba sebagai fitur klasifikasi pembanding TF-IDF.
