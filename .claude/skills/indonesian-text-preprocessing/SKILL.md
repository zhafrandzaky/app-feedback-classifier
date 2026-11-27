---
name: indonesian-text-preprocessing
description: Preprocessing pipeline for informal Indonesian app reviews (case folding, regex cleaning, slang normalization, tokenization, stopword removal with negation kept, Sastrawi stemming). Use when writing or reviewing notebook 01 or any code that produces clean_text.
---

# Indonesian Text Preprocessing

## Pipeline order

1. Case folding.
2. Regex cleaning: URL, HTML tags, mentions and hashtags, digits, emoji and symbols, repeated characters, extra whitespace.
3. Slang normalization (before stopword removal, so `gk` becomes `tidak` first).
4. Tokenization (whitespace split after cleaning is sufficient; `nltk.word_tokenize` optional).
5. Stopword removal with an explicit keep list.
6. Stemming with Sastrawi.
7. Join tokens to `clean_text`. Drop rows whose `clean_text` is empty and report how many.

Keep every intermediate column so the before/after table can be shown.

## Reference implementation

```python
import re
from Sastrawi.Stemmer.StemmerFactory import StemmerFactory
from Sastrawi.StopWordRemover.StopWordRemoverFactory import StopWordRemoverFactory

RE_URL = re.compile(r'https?://\S+|www\.\S+')
RE_HTML = re.compile(r'<.*?>')
RE_MENTION = re.compile(r'[@#]\w+')
RE_DIGIT = re.compile(r'\d+')
RE_NON_ALPHA = re.compile(r'[^a-z\s]')
RE_REPEAT = re.compile(r'(.)\1{2,}')
RE_SPACE = re.compile(r'\s+')

def clean_text(text: str) -> str:
    t = RE_URL.sub(' ', text)
    t = RE_HTML.sub(' ', t)
    t = RE_MENTION.sub(' ', t)
    t = RE_DIGIT.sub(' ', t)
    t = RE_NON_ALPHA.sub(' ', t)      # removes emoji, punctuation, symbols
    t = RE_REPEAT.sub(r'\1', t)       # 'lemooot' -> 'lemot'
    return RE_SPACE.sub(' ', t).strip()

KEEP = {'tidak', 'belum', 'bukan', 'jangan', 'kurang', 'bisa', 'gagal', 'lama', 'cepat', 'lambat', 'baru'}
STOPWORDS = set(StopWordRemoverFactory().get_stop_words()) - KEEP

stemmer = StemmerFactory().create_stemmer()
_stem_cache: dict[str, str] = {}

def stem(token: str) -> str:
    if token not in _stem_cache:
        _stem_cache[token] = stemmer.stem(token)
    return _stem_cache[token]
```

Notes:

- `RE_REPEAT` with `\1` collapses to one character. Notebook 00 used `\1\1` for keyword matching only; notebook 01 may differ, and the difference must be explained.
- Apply `clean_text` after `str.lower()`, because `RE_NON_ALPHA` assumes lowercase.
- Sastrawi is slow. Always stem unique tokens through the cache.

## Slang dictionary

- Start from the `SLANG` dictionary in notebook 00 so labeling and preprocessing agree.
- Optional extension: `colloquial-indonesian-lexicon.csv` from https://github.com/nasalsabila/kamus-alay. Inspect it before use and remove mappings that change meaning in this domain (for example mappings for `app`, `pin`, `otp`).
- Store the final dictionary in the notebook or a small CSV committed to the repo, never fetched silently at run time without a fallback.

## Domain words to protect

Technical loanwords carry the label signal and must survive cleaning: `error`, `bug`, `crash`, `loading`, `lag`, `hang`, `update`, `login`, `otp`, `pin`, `ui`, `ux`, `dark mode`. Check they are not in the stopword list and that stemming leaves them intact.

## What to show in the notebook

- A before/after table for five reviews across every intermediate column.
- A token table (tokens, after stopword, after stemming) for the same reviews.
- A regex table: pattern, meaning, example before, example after.
- Counts: vocabulary size before and after, average words per review before and after, rows dropped as empty.
