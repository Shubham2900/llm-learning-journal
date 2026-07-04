# Debugging My First vLLM Setup

## Objective

My goal was simple: run a small language model locally using vLLM and understand how OpenAI-compatible model serving works.

I expected this to be a short installation exercise.

It turned into one of the most educational debugging sessions I've had.

---

## Journey

### 1. Started on Windows

Initially, I tried running vLLM directly on Windows.

The first issue I encountered was a missing dependency (`uvloop`). While investigating, I learned that the real challenge wasn't the missing package—it was the overall compatibility of Windows and Python 3.14 with the current vLLM ecosystem.

**Lesson:** Sometimes the first error is only a symptom.

---

### 2. Moving to WSL

Since most AI tooling targets Linux, I moved to WSL2.

This immediately aligned my development environment with what most ML engineers use in practice.

**Lesson:** Choosing the right environment is often more important than fixing individual errors.

---

### 3. Ubuntu 26.04 Challenges

Ubuntu 26.04 shipped with Python 3.14, which introduced compatibility issues with several AI libraries.

I attempted to install Python 3.11 manually using `pyenv`, which exposed me to Python's build process and its system dependencies.

During compilation I encountered missing modules like:

- `_ssl`
- `_bz2`
- `_ctypes`
- `readline`

This helped me understand that Python itself depends on several native system libraries.

**Lesson:** Python isn't a single executable—it's built on top of many system libraries.

---

### 4. Fresh Start

Rather than continuing to patch the environment, I installed Ubuntu 24.04 alongside Ubuntu 26.04.

This proved to be a much smoother experience.

**Lesson:** Sometimes rebuilding is more efficient than endlessly debugging.

---

### 5. Virtual Environments

My first virtual environment was incomplete.

Instead of trying to repair it, I deleted it and recreated it correctly.

This reinforced that virtual environments are simply isolated directories containing Python and project dependencies.

**Lesson:** Not every problem needs a complicated solution.

---

### 6. Installing vLLM

With Ubuntu 24.04 and Python 3.12, vLLM installed successfully.

This marked the first major milestone of the project.

---

### 7. GPU Compatibility

PyTorch successfully detected my GPU:

- NVIDIA GTX 1050 Ti
- Compute Capability 6.1

However, the latest PyTorch binaries bundled with modern vLLM no longer include kernels for this GPU architecture.

The result was an important realization:

The GPU was visible.

The GPU was **not supported**.

Those are two different things.

**Lesson:** Detection does not imply compatibility.

---

### 8. CPU Attempt

I attempted to run vLLM on the CPU instead.

Although the server started, the current version of vLLM still initialized CUDA internally and failed before serving requests.

Rather than spending hours downgrading libraries and building older versions from source, I decided to continue my learning using cloud GPUs.

---

## Biggest Takeaway

The most valuable lesson wasn't about CUDA or vLLM.

It was learning to debug software one layer at a time.

```
Application
      │
      ▼
vLLM
      │
      ▼
PyTorch
      │
      ▼
CUDA
      │
      ▼
NVIDIA Driver
      │
      ▼
GPU
```

When something failed, the question became:

- Is this layer working?
- If yes, move down.
- If no, investigate here.

This systematic approach made debugging far less overwhelming.

---

## What I Learned

- How WSL fits into AI development
- Why Linux is the preferred environment for ML
- The purpose of Python virtual environments
- How Python depends on native system libraries
- How CUDA, PyTorch, and GPU architectures interact
- Why Compute Capability matters
- The importance of debugging layer by layer

---

## Next Step

I'll continue exploring vLLM using a cloud GPU environment while applying the lessons learned from this setup journey.