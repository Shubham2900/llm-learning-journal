# Fine-Tuning BERT for Sentence Pair Classification using Hugging Face Trainer

This project demonstrates how to fine-tune a pre-trained **BERT** model on the **GLUE MRPC (Microsoft Research Paraphrase Corpus)** dataset using the **Hugging Face Transformers Trainer API**.

The notebook covers the complete fine-tuning pipeline—from loading the dataset to evaluating the trained model.

---

## 📌 Project Overview

The objective is to train a BERT model to determine whether two given sentences are paraphrases of each other.

The notebook includes:

- Loading the GLUE MRPC dataset
- Tokenizing sentence pairs
- Dynamic padding using a Data Collator
- Loading a pre-trained BERT model
- Configuring training arguments
- Fine-tuning using the Hugging Face Trainer API
- Evaluating the trained model using GLUE metrics

---

## 📂 Dataset

**Dataset:** GLUE - MRPC (Microsoft Research Paraphrase Corpus)

Task:
Binary Sequence Classification

Input:
- Sentence 1
- Sentence 2

Output:
- 0 → Not Paraphrases
- 1 → Paraphrases

Dataset is loaded directly from Hugging Face:

```python
load_dataset("glue", "mrpc")
```

---

## 🤖 Model

Pre-trained model:

```
bert-base-uncased
```

The notebook fine-tunes the model using:

```python
AutoModelForSequenceClassification
```

with

```python
num_labels=2
```

---

## 🛠 Libraries Used

- transformers
- datasets
- evaluate
- huggingface_hub
- numpy

Install all dependencies:

```bash
pip install -U datasets transformers huggingface_hub accelerate evaluate
```

---

## 📖 Workflow

### 1. Load Dataset

```python
load_dataset("glue", "mrpc")
```

---

### 2. Load Tokenizer

```python
AutoTokenizer.from_pretrained("bert-base-uncased")
```

---

### 3. Tokenize Sentence Pairs

Each pair of sentences is tokenized using

```python
tokenizer(sentence1, sentence2)
```

with truncation enabled.

---

### 4. Dynamic Padding

The notebook uses

```python
DataCollatorWithPadding
```

to dynamically pad batches during training, improving memory efficiency.

---

### 5. Load the Pre-trained Model

```python
AutoModelForSequenceClassification
```

configured for binary classification.

---

### 6. Configure Training

Training is configured using

```python
TrainingArguments
```

including evaluation after every epoch.

---

### 7. Fine-Tune the Model

Training is performed using the Hugging Face

```python
Trainer
```

API.

---

### 8. Evaluate Performance

The notebook evaluates the model using the GLUE MRPC evaluation metric.

Metrics include:

- Accuracy
- F1 Score

---

## 📊 Model Pipeline

```
GLUE MRPC Dataset
        │
        ▼
Sentence Pair Tokenization
        │
        ▼
Dynamic Padding
        │
        ▼
BERT Base Uncased
        │
        ▼
Trainer API
        │
        ▼
Fine-Tuned Model
        │
        ▼
Evaluation (Accuracy & F1)
```

---

## 📁 Files

```
Fine_tuning_a_model_with_the_Trainer_API_or_Keras.ipynb
README.md
```

---

## 🚀 Learning Outcomes

By completing this notebook, you will understand:

- How Hugging Face datasets work
- Sequence pair classification
- Tokenization with AutoTokenizer
- Dynamic padding
- Loading pre-trained transformer models
- Fine-tuning with the Trainer API
- Training configuration using TrainingArguments
- Model evaluation using the Evaluate library

---

## ⚠️ Common Issues

### HfUriError

If you encounter:

```
HfUriError
```

or

```
Invalid HF URI
```

upgrade all Hugging Face libraries:

```bash
pip install -U datasets transformers huggingface_hub accelerate evaluate
```

and restart the notebook kernel.

---

## 📚 References

- Hugging Face Transformers
- Hugging Face Datasets
- Hugging Face Evaluate
- GLUE Benchmark
- BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding