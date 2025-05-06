---
draft: false
date: 2025-05-05
authors:
  - me
categories:
  - GenAI
---

# Running LLMs Locally
This is a short post on how I've setup llms to run locally.<!-- more -->

## Prerequisites
There are two tools that make this trivial: [UV](https://docs.astral.sh/uv/), and [LLM](https://llm.datasette.io/en/stable/). Start by installing them:

```bash
# Install UV:
curl -LsSf https://astral.sh/uv/install.sh | sh

# Set Python 3.12 as the default:
export UV_PYTHON=3.12
source ~/.bashrc

# Install LLM as a UV tool:
uv tool install llm --python 3.12
```

Corey Schafer has a great overview of UV, and why you may choose to use it [Corey Schafer - Python Tutorial: UV](https://youtu.be/AMdG7IjgSPM?si=duXrWjJDuAjMZtp1)  

Detailed installation documentation for LLM and UV:

 - [UV Installation](https://docs.astral.sh/uv/getting-started/installation/)
 - [LLM Installation](https://llm.datasette.io/en/stable/setup.html)

## Installing the Models
Apple's [MLX](https://opensource.apple.com/projects/mlx/) ([GH](https://github.com/ml-explore/mlx)) is a framework for running ML workloads efficiently on Apple silicon. It's the underlying technology that makes this all work so well (even on my 4yo macbook). Simon talks about this in a bit more [detail](https://x.com/simonw/status/1904213763454337127):

> The challenge with these models is not RAM, it's GPU-available RAM. Apple Silicon makes system RAM available to the GPU, which is why Apple hardware (thanks to MLX) is good for running these models. On non-Apple machines I think you would need 600+ GB or VRAM to run this model [DeepSeek v3 0324]

The LLM tool we installed before has a [plugin for MLX](https://github.com/simonw/llm-mlx). We'll use this in tandem with models from Hugging Face.

```bash
# Install the plugin
llm install llm-mlx

# Download two models
llm mlx download-model mlx-community/gemma-3-27b-it-qat-4bit
llm mlx download-model mlx-community/Qwen3-8B-4bit

# Setup aliases
llm aliases set q3 mlx-community/Qwen3-8B-4bit
llm aliases set g3 mlx-community/gemma-3-27b-it-qat-4bit
```

## Usage
Now we can use the models like so:

```bash
tlofreso@MacBook-Pro-3:~$ llm -m g3 "What is the meaning of life? Answer in a single number."
42. 
```

## The Models
The models I chose are from the Gemma3 and Qwen3 families. I chose them due to the consensus on Twitter/X being these are two of the most capable models that will fit on my 32GB macbook. Gemma weighs in at around 14GB of memory consumption, and Qwen slightly under 5GB.

## Links
 - MLX on Hugging Face: [MLX Community](https://huggingface.co/mlx-community)
 - Gemma3 Family [Models](https://ai.google.dev/gemma)
 - Quen3 Family [Models](https://qwenlm.github.io/blog/qwen3/)
 - Simon on Gemma3: [Gemma3 QAT Models](https://simonwillison.net/2025/Apr/19/gemma-3-qat-models/)
 - Simon on [Qwen3](https://simonwillison.net/2025/Apr/29/qwen-3/)

## Credits
I referenced two personalities in this post that I've wrote about before. [Simon Willison](../people-i-follow.md/#simon-willison) and [Corey Schafer](../people-i-follow.md/#corey-schafer). Just wanted to highlight here that they continue to be a steadfast source of great information.