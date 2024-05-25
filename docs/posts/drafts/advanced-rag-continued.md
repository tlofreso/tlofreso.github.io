---
draft: true
date: 2024-05-20
authors:
  - me
categories:
  - GenAI
---

# Advanced RAG Continued
Continuing with RAG, I've been diving in on a number of other readings in the LlamaIndex docs.

The best starting point, is [High-Level Concepts (RAG)](https://docs.llamaindex.ai/en/stable/getting_started/concepts/).  
After High-Level Concepts read: [Advanced Topics](https://docs.llamaindex.ai/en/stable/optimizing/production_rag/). The [Basic Strategies](https://docs.llamaindex.ai/en/stable/optimizing/basic_strategies/basic_strategies/) page covers several approachable ways to level-up your RAG applications. One of the first strategies they discuss is the ideal Chunk Size. Here's a well-written technical blog post covering ways to find the ideal chunk size using Llama's Response Evaluation Module. [Evaluating the Ideal Chunk Size for a RAG System using LlamaIndex](https://www.llamaindex.ai/blog/evaluating-the-ideal-chunk-size-for-a-rag-system-using-llamaindex-6207e5d3fec5)

And important note:

> NOTE: Unlike an LLM (which you can change at any time), if you change your embedding model, you must re-index your data. Furthermore, you should ensure the same embedding model is used for both indexing and querying.

"Multi-Tenancy RAG" is the term for RBAC RAG. Basically via the use of metadata filtering.