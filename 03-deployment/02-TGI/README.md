# Text Generation Inference (TGI)

This project explores **Hugging Face Text Generation Inference (TGI)**, a production-grade inference server designed for serving Large Language Models (LLMs) with high throughput and low latency.

Unlike the Transformers library, which is primarily intended for model development and experimentation, TGI is optimized for production deployments and exposes an OpenAI-compatible API.

---

## Objective

- Learn how to deploy an LLM using TGI
- Understand Docker-based model serving
- Compare TGI with vLLM and Transformers
- Investigate hardware compatibility requirements

---

## Environment

| Component | Value |
|-----------|-------|
| OS | Windows 10 Pro |
| Docker | Docker Desktop |
| GPU | NVIDIA GeForce GTX 1050 Ti |
| Compute Capability | 6.1 |
| CUDA Driver | 13.0 |
| Docker Runtime | NVIDIA Container Runtime |

---

## Pull the Docker Image

```bash
docker pull ghcr.io/huggingface/text-generation-inference:latest
```

---

## Run the Server

```bash
docker run --gpus all ^
  --shm-size 1g ^
  -p 8080:80 ^
  -v tgi-data:/data ^
  ghcr.io/huggingface/text-generation-inference:latest ^
  --model-id HuggingFaceTB/SmolLM2-360M-Instruct
```

> **Note:** On Windows CMD, use `^` for line continuation or place the command on a single line.

---

## What Happened?

The container successfully:

- Started Docker
- Downloaded the model
- Initialized the inference server
- Began loading model weights

However, model initialization failed with:

```text
RuntimeError: CUDA error: no kernel image is available for execution on the device
```

---

## Root Cause

The local GPU is:

- NVIDIA GTX 1050 Ti
- Compute Capability **6.1 (Pascal)**

Modern TGI Docker images are built for newer NVIDIA GPU architectures and include CUDA kernels that are **not compiled for Pascal GPUs**.

As a result, CUDA cannot execute the required kernels, causing the server to terminate during model loading.

---

## Key Learning

This was **not** caused by:

- ❌ Docker
- ❌ CUDA installation
- ❌ NVIDIA drivers
- ❌ Model weights
- ❌ Hugging Face

Instead, it was a **hardware compatibility limitation**.

The Docker container launched successfully, but the GPU architecture was unsupported by the compiled CUDA kernels inside the image.

---

## Comparison with Previous Experiments

| Framework | GTX 1050 Ti (SM 6.1) | Tesla T4 (SM 7.5) |
|-----------|----------------------|-------------------|
| Transformers | ✅ Works | ✅ Works |
| vLLM | ❌ Unsupported | ✅ Works |
| TGI | ❌ Unsupported | Expected to work |

---

## What I Learned

- TGI is a production inference server built around Docker.
- Docker Desktop with GPU passthrough was configured successfully.
- NVIDIA Container Toolkit was functioning correctly.
- Production inference frameworks increasingly optimize for modern GPU architectures.
- Older GPUs (Pascal / Compute Capability 6.x) are no longer fully supported by default builds.

---

## Conclusion

Although I could not complete a successful inference run on my local GTX 1050 Ti, the exercise provided valuable insights into production model serving and hardware compatibility.

This experiment reinforced that deploying LLMs is not only about installing software—it also requires understanding GPU architectures, CUDA compatibility, and the hardware assumptions made by modern inference frameworks.

---

## Next Steps

- Explore **llama.cpp** for efficient inference on consumer hardware.
- Compare Transformers, vLLM, TGI, and llama.cpp across:
  - Architecture
  - Performance
  - Ease of deployment
  - Hardware requirements