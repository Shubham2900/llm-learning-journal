# Deploying LLMs with llama.cpp

This project demonstrates how to deploy a quantized Large Language Model (LLM) locally using **llama.cpp**, Docker, NVIDIA GPU acceleration, and Cloudflare Tunnel.

## Objective

Learn how to:

- Run GGUF models locally
- Serve models through llama.cpp
- Use GPU acceleration with CUDA
- Deploy inside Docker
- Expose the model over the internet
- Interact through the built-in Chat UI and OpenAI-compatible API

---

# Architecture

```
                    Internet
                         │
                         ▼
                Cloudflare Tunnel
                         │
                         ▼
                llama.cpp Server
                         │
                         ▼
                 TinyLlama GGUF Model
                         │
                         ▼
                    NVIDIA GPU
```

---

# Technologies Used

- llama.cpp
- GGUF Models
- Docker
- NVIDIA CUDA
- Cloudflare Tunnel
- OpenAI Compatible REST API

---

# Project Structure

```
06-llama_cpp/
│
├── README.md
├── notebook.ipynb
```

---

# Prerequisites

- Windows 10/11
- Docker Desktop
- NVIDIA GPU
- NVIDIA Container Toolkit
- Docker GPU support enabled

Verify Docker GPU access:

```bash
docker run --rm --gpus all nvidia/cuda:12.8.0-runtime-ubuntu22.04 nvidia-smi
```

---

# Step 1 — Download a GGUF Model

Example:

TinyLlama 1.1B Chat

```
python -m huggingface_hub download TheBloke/TinyLlama-1.1B-Chat-v1.0-GGUF tinyllama-1.1b-chat-v1.0.Q2_K.gguf --local-dir C:\Users\Shubham\llama_models\models
```

---

# Step 2 — Start llama.cpp

```bash
docker run --rm --gpus all -p 8080:8080 -v C:\Users\Shubham\llama_models\models:/models ghcr.io/ggml-org/03-llama.cpp:server-cuda -m /models/tinyllama-1.1b-chat-v1.0.Q2_K.gguf
```

---

# Step 3 — Open the Chat Interface

Visit

```
http://localhost:8080
```

You should see the built-in chat interface.

---

# Step 4 — Test the REST API

List available models

```bash
curl http://localhost:8080/v1/models
```

Chat Completion

```bash
curl -X POST http://localhost:8080/v1/chat/completions ^
-H "Content-Type: application/json" ^
-d "{\"messages\":[{\"role\":\"user\",\"content\":\"Explain Transformers in one sentence.\"}]}"
```

---

# Step 5 — Expose Publicly

Install Cloudflare Tunnel

```bash
winget install Cloudflare.cloudflared
```

Create a temporary public endpoint

```bash
cloudflared tunnel --url http://localhost:8080
```

Cloudflare returns a public HTTPS URL.

```
https://xxxx.trycloudflare.com
```

Now anyone can interact with your local model securely.

---

# What I Learned

- GGUF is an optimized format for local inference.
- llama.cpp can efficiently run quantized models.
- Docker provides a portable deployment environment.
- CUDA enables GPU acceleration.
- llama.cpp exposes an OpenAI-compatible REST API.
- Cloudflare Tunnel securely exposes a local service without opening firewall ports.

---

# Next Steps

- Deploy larger models
- Reverse proxy with Nginx
- Authentication
- Cloud GPU deployment
- Load balancing multiple inference servers
