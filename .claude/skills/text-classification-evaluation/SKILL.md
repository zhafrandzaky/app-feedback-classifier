---
name: text-classification-evaluation
description: How to train, compare, and evaluate classifiers for the five-label review dataset (BoW/TF-IDF with NB, SVM, LR, RF in Part 1; LSTM and IndoBERT in Part 2), including imbalance handling and error analysis. Use when writing notebooks 02, 03, or any Part 2 modeling notebook.
---

# Text Classification and Evaluation

## Data split

- One stratified split for the whole project: `train_test_split(test_size=0.2, stratify=y, random_state=42)`.
- Save the split indices (or the split CSVs) so every later notebook, including Part 2 models, uses the same test set.
- Fit vectorizers, scalers, and tokenizers on the training set only.

## Part 1 models

Use scikit-learn `Pipeline` objects so vectorizer and model travel together.

```python
from sklearn.pipeline import Pipeline
from sklearn.feature_extraction.text import CountVectorizer, TfidfVectorizer
from sklearn.naive_bayes import MultinomialNB
from sklearn.svm import LinearSVC
from sklearn.linear_model import LogisticRegression
from sklearn.ensemble import RandomForestClassifier

VECTORIZERS = {
    'BoW': lambda: CountVectorizer(ngram_range=(1, 2), min_df=2),
    'TF-IDF': lambda: TfidfVectorizer(ngram_range=(1, 2), min_df=2, sublinear_tf=True),
}
MODELS = {
    'Naive Bayes': lambda: MultinomialNB(),
    'SVM': lambda: LinearSVC(class_weight='balanced', random_state=42),
    'Logistic Regression': lambda: LogisticRegression(class_weight='balanced', max_iter=1000, random_state=42),
    'Random Forest': lambda: RandomForestClassifier(n_estimators=300, class_weight='balanced', n_jobs=-1, random_state=42),
}
```

Train every model on every vectorizer and collect results in one DataFrame.

## Metrics

- Primary: macro F1. Also report accuracy, weighted F1, and per-class precision, recall, F1 from `classification_report(..., target_names=LABELS, digits=4)`.
- Confusion matrix with `labels=LABELS` so the order is fixed, shown as a heatmap with counts, and a row-normalized version.
- Never claim a model is best on accuracy alone when classes are imbalanced.

## Error analysis (required in the report)

1. Identify the most confused label pairs from the confusion matrix.
2. Show 5 to 10 misclassified test reviews: text, true label, predicted label, short reason.
3. Top features per class from Logistic Regression or linear SVM coefficients.
4. State the label-leakage caveat: labels come from keyword rules, so models partly relearn those keywords. Suggest a small manually labeled check set as future work.

## Part 2 guidance

- Baseline is the best Part 1 pipeline on the same test set.
- Deep learning: Keras or PyTorch BiLSTM with an embedding layer, optional Word2Vec initialization from notebook 04. Use early stopping on a validation split carved from the training set.
- Transformer: fine-tune `indobenchmark/indobert-base-p1` (or IndoBERTweet for informal text) with Hugging Face `Trainer`, `max_length` around 128, 3 to 5 epochs, learning rate 2e-5 to 5e-5, weighted loss or class weights for imbalance, GPU accelerator on Kaggle.
- Report the same metrics and confusion matrix as Part 1 so results are comparable in one table.
- Export the final model for the app (joblib for scikit-learn, `save_pretrained` or ONNX for Transformers) and record where it is stored in `models/README.md`.
