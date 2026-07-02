# Tokenization

Large Language Models (LLMs) cannot process raw text directly. Before a model can understand text, it must first convert it into **tokens**, which are then mapped to numerical IDs.

## Types of Tokenization

### 1. Word-Based Tokenization

Each word is treated as a separate token.

**Example**

```text
Input: "Dogs are friendly"

Tokens:
["Dogs", "are", "friendly"]
```

**Advantages**

* Simple and intuitive
* Easy to implement

**Drawbacks**

* Requires a very large vocabulary
* Produces many Out-of-Vocabulary (OOV) tokens for unseen words
* Does not capture relationships between similar words (e.g., *play*, *playing*, *played*)

---

### 2. Character-Based Tokenization

Each character is treated as an individual token.

**Example**

```text
Input: "Dog"

Tokens:
["D", "o", "g"]
```

**Advantages**

* Small vocabulary
* Can represent any word without OOV issues

**Drawbacks**

* Produces much longer input sequences
* Individual characters carry very little semantic meaning

---

### 3. Subword-Based Tokenization

Subword tokenization combines the advantages of word-based and character-based tokenization. Instead of treating every word as unique, it splits words into meaningful subword units.

**Examples**

```text
Dogs      → Dog + s

Playing   → Play + ing

Unhappy   → Un + happy
```

**Advantages**

* Smaller and more efficient vocabulary
* Handles unseen words effectively
* Preserves the meaning of common roots, prefixes, and suffixes
* Used by most modern Large Language Models

## Key Takeaways

* LLMs operate on tokens, not raw text.
* Word-based tokenization struggles with vocabulary size.
* Character-based tokenization creates long sequences.
* Subword tokenization provides the best balance between vocabulary size and language understanding, making it the preferred choice for modern transformer models.
