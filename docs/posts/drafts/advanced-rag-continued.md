---
draft: true
date: 2024-05-20
authors:
  - me
categories:
  - GenAI
---

# Advanced RAG (Continued)
Something I've learned since the [last post](../advanced-rag.md), one RAG does not fit all.

The focus for this post will be on Long Context RAG, coined in late 2023 as 'Needle in a Haystack[^1]' analysis by [Greg Kamradt](https://x.com/GregKamradt). The idea is you've got a large quantity of data, and you seed that data with a known fact, then try to retrieve it. Doing this reliably is not trivial, even for today's best models. In addition, the analysis can be costly.(1) Some have expanded on Greg's work with multi-Needle experiments.[^2]
{ .annotate }

1.  ![Cost](../../assets/posts/advanced-rag-continued/cost-dark.png#only-dark)
    ![Cost](../../assets/posts/advanced-rag-continued/cost-light.png#only-light)





[^1]: The first instance of "needle in a haystack" analysis: [here](https://x.com/GregKamradt/status/1722386725635580292)
[^2]: Work from [Lance Martin](https://twitter.com/RLanceMartin) using LangChain to perform [Multi Needle in a Haystack](https://github.com/gkamradt/LLMTest_NeedleInAHaystack/blob/main/viz/CreateVisFromLangSmithTesting.ipynb)

## Long Context Retrieval


Continuing with RAG, I've been diving in on a number of other readings in the LlamaIndex docs.

The best starting point, is [High-Level Concepts (RAG)](https://docs.llamaindex.ai/en/stable/getting_started/concepts/).  
After High-Level Concepts read: [Advanced Topics](https://docs.llamaindex.ai/en/stable/optimizing/production_rag/). The [Basic Strategies](https://docs.llamaindex.ai/en/stable/optimizing/basic_strategies/basic_strategies/) page covers several approachable ways to level-up your RAG applications. One of the first strategies they discuss is the ideal Chunk Size. Here's a well-written technical blog post covering ways to find the ideal chunk size using Llama's Response Evaluation Module. [Evaluating the Ideal Chunk Size for a RAG System using LlamaIndex](https://www.llamaindex.ai/blog/evaluating-the-ideal-chunk-size-for-a-rag-system-using-llamaindex-6207e5d3fec5)

An important note:

> NOTE: Unlike an LLM (which you can change at any time), if you change your embedding model, you must re-index your data. Furthermore, you should ensure the same embedding model is used for both indexing and querying.

"Multi-Tenancy RAG" is the term for RBAC RAG. Basically via the use of metadata filtering.

### Needle in a Haystack RAG

A test by [Greg Kamradt](https://x.com/GregKamradt) ([website](https://www.gregkamradt.com)) via [this](https://x.com/GregKamradt/status/1722386725635580292) tweet. Here's the project on GH: [Needle in a Haystack](https://github.com/gkamradt/LLMTest_NeedleInAHaystack/tree/main)

Later, folks tried Multi-Needle in a Haystack via [this](https://x.com/RLanceMartin/status/1767950505148563890) tweet. And here's a longer blog post discussing the strategy: [multi-needle in a haystack](https://blog.langchain.dev/multi-needle-in-a-haystack/)

This is a pretty good thread for production rag: https://www.reddit.com/r/MachineLearning/comments/1b244vc/comment/ksj9gin/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button

## Use the right tool for the job
I've found, it's important that devs not rely too heavily on the LLM to answer questions on a knowledge base. Knowing what you're looking for, and simply grabbing the context around that thing can be really helpful.