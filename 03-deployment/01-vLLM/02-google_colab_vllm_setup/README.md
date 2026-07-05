# 🚀 Running SmolLM2 with vLLM on Google Colab (Tesla T4)

This notebook demonstrates how to serve an open-source Large Language Model using **vLLM** on a **Google Colab Tesla T4 GPU** through an **OpenAI-compatible API**.

The goal is to understand how production LLM inference works—from downloading a model to serving it over HTTP and querying it using the OpenAI Python SDK.

---

# 📌 Project Overview

**Model:** `HuggingFaceTB/SmolLM2-360M-Instruct`

**Inference Engine:** vLLM

**GPU:** NVIDIA Tesla T4 (16 GB VRAM)

**Framework:** PyTorch

**Interface:** OpenAI-compatible REST API

---

# 🏗 Architecture

```
                User Request
                     │
                     ▼
         OpenAI Python Client
                     │
                     ▼
      http://127.0.0.1:8000/v1
                     │
                     ▼
          vLLM API Server
                     │
                     ▼
            vLLM Engine
                     │
                     ▼
      SmolLM2-360M-Instruct
                     │
                     ▼
               Tesla T4 GPU
```

---

# 📦 Installation

```bash
pip install -U \
vllm==0.10.2 \
torch \
transformers \
accelerate \
openai
```

---

# ▶️ Starting the API Server

```bash
python -m vllm.entrypoints.openai.api_server \
    --host 0.0.0.0 \
    --model HuggingFaceTB/SmolLM2-360M-Instruct
```

During startup, vLLM performs the following steps:

- Detects available GPU
- Downloads model configuration
- Downloads tokenizer
- Downloads model weights
- Loads weights into GPU memory
- Builds the KV Cache
- Compiles optimized kernels
- Captures CUDA Graphs
- Starts an OpenAI-compatible REST API

---

# ✅ Verify the Server

Check that the API server is running:

```bash
curl http://127.0.0.1:8000/v1/models
```

Expected response:

```json
{
  "object": "list",
  "data": [
    {
      "id": "HuggingFaceTB/SmolLM2-360M-Instruct"
    }
  ]
}
```

---

# 💬 Sending a Chat Request

```python
from openai import OpenAI

client = OpenAI(
    api_key="EMPTY",
    base_url="http://127.0.0.1:8000/v1",
)

response = client.chat.completions.create(
    model="HuggingFaceTB/SmolLM2-360M-Instruct",
    messages=[
        {
            "role": "system",
            "content": "You are a helpful assistant."
        },
        {
            "role": "user",
            "content": "Who are you?"
        }
    ],
    temperature=0.7,
    max_tokens=100,
)

print(response.choices[0].message.content)
```

---

# 📁 API Endpoints

| Endpoint | Purpose |
|-----------|---------|
| `/v1/models` | List available models |
| `/v1/chat/completions` | Chat interface |
| `/v1/completions` | Text completion |
| `/v1/embeddings` | Generate embeddings |
| `/metrics` | Prometheus metrics |

---

# 🔍 What Happens Internally?

When the server starts, vLLM:

1. Detects the GPU.
2. Downloads the model from Hugging Face.
3. Loads tokenizer files.
4. Loads model weights into GPU memory.
5. Creates the KV Cache.
6. Compiles optimized inference kernels.
7. Captures CUDA Graphs to reduce CPU overhead.
8. Starts an HTTP server exposing OpenAI-compatible APIs.

---

# ⚠️ Warnings Encountered

## bfloat16 Warning

```
Tesla T4 doesn't support bfloat16.
Falling back to float16.
```

This is expected because Tesla T4 (Compute Capability 7.5) supports FP16 but not BF16.

---

## FlashAttention 2 Warning

```
FA2 is only supported on compute capability >= 8
```

Tesla T4 cannot use FlashAttention 2, so vLLM automatically falls back to FlexAttention/PyTorch implementations.

This is normal behavior.

---

## FlashInfer Warning

```
FlashInfer is not available.
```

The server still works correctly. FlashInfer is an optional optimization library.

---

# 🛠 Debugging Journey

During setup, several issues were encountered and resolved:

- GPU compatibility issues on an older GTX 1050 Ti (Compute Capability 6.1)
- PyTorch build incompatible with older CUDA architectures
- CUDA runtime (`libcudart.so.13`) mismatch
- Version incompatibilities between vLLM, Transformers, and PyTorch
- Tokenizer compatibility errors
- Verifying server availability using:
  - `ps`
  - `ss`
  - `curl`
- Confirming that the API server was successfully listening on port **8000**

These debugging steps reinforced the importance of checking software versions, GPU architecture compatibility, and validating each layer of the inference stack.

---

# 📚 Key Concepts Learned

- vLLM architecture
- OpenAI-compatible inference servers
- Model serving workflows
- CUDA Graphs
- KV Cache
- GPU memory allocation
- FP16 vs BF16
- REST APIs for LLM inference
- Model loading pipeline
- API testing with `curl`
- Using the OpenAI SDK with locally hosted models

---

# 🎯 Outcome

Successfully served **SmolLM2-360M-Instruct** using **vLLM** on a **Tesla T4 GPU** and interacted with it through an **OpenAI-compatible API**.

This setup mirrors the architecture commonly used for production LLM inference systems and provides a practical foundation for deploying and serving open-source language models.

---

# 🙏 Acknowledgements

- Hugging Face for the SmolLM2 model
- vLLM Project for the high-performance inference engine
- Google Colab for providing Tesla T4 GPUs