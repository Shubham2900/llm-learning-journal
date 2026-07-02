# Padding

Transformer models process inputs in batches. When multiple sentences are passed to a model, each sentence is first tokenized into a sequence of token IDs.

Since different sentences typically produce different numbers of tokens, their sequences have varying lengths.

## Example

```text
Sentence 1: "I love Transformers!"
Token IDs: [101, 1045, 2293, 19081, 999, 102]

Sentence 2: "Hello"
Token IDs: [101, 7592, 102]
```

Deep learning frameworks such as PyTorch require batched inputs to have a consistent shape. Therefore, shorter sequences are extended using **padding tokens (`[PAD]`)**.

After padding:

```text
Sentence 1: [101, 1045, 2293, 19081, 999, 102]

Sentence 2: [101, 7592, 102, [PAD], [PAD], [PAD]]
```

Both sequences now have the same length and can be represented as a tensor with the shape:

```text
(batch_size, sequence_length)
```

## Why is Padding Required?

* Enables multiple sequences to be processed together in a single batch.
* Produces tensors with a consistent shape, which deep learning frameworks require.
* Improves computational efficiency through parallel processing.

---

# Attention Mask

Padding tokens do not contain meaningful information and should not contribute to the model's predictions. To ensure this, an **attention mask** is created alongside the input IDs.

The attention mask tells the model which tokens are real and which are padding.

**Example**

```text
Input IDs:
[101, 7592, 102, 0, 0, 0]

Attention Mask:
[1, 1, 1, 0, 0, 0]
```

Where:

* `1` → Actual token (attend to it)
* `0` → Padding token (ignore it)

When using the Hugging Face tokenizer, the attention mask is generated automatically.

```python
from transformers import AutoTokenizer

checkpoint = "distilbert-base-uncased-finetuned-sst-2-english"
tokenizer = AutoTokenizer.from_pretrained(checkpoint)

sentences = [
    "I've been waiting for a Hugging Face course my whole life.",
    "I love Transformers!"
]

inputs = tokenizer(
    sentences,
    padding=True,
    truncation=True,
    return_tensors="pt"
)

print(inputs["input_ids"])
print(inputs["attention_mask"])
```

The output will contain both the padded token IDs and their corresponding attention masks.

---

# Further Reading

For a more detailed explanation of padding, truncation, and attention masks, refer to the official Hugging Face course:

https://huggingface.co/learn/llm-course/chapter2/5
