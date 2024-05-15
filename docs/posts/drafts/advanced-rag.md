---
draft: true
date: 2024-05-15
authors:
  - me
categories:
  - GenAI
---

# Advanced RAG
First things first... I titled this "Advanced RAG". This is not advanced RAG. But it does mark an evolution in my ability, and feels advanced to 2024 Tony.

For my use case, I have a few goals:

1. Ingest custom data
2. Return accurate responses to user prompts
3. Always include a citation in the response

I'll be using [LlamaIndex](https://www.llamaindex.ai/open-source) to accomplish this.

## Learning as I Go
The other day, I came across [this post](https://twitter.com/llama_index/status/1788686110593368509). It's a video of [El Risitas](https://en.wikipedia.org/wiki/El_Risitas) guy, and the subtitles convey all the _laughably ridiculous_ things people writing RAG apps today are doing.

> They split the file into chunks by page, or by paragraph or who the fuck knows. Embed it with `ada-002` for a couple cents. Store the embeddings in some hacked together database. The[y] match your query by cosine similarity :laughing: and return the top k. :rofl: If the answer is bad ... they just increase the value of k! :laughing: :rofl: Users have no idea how embeddings work, so they fill their query full of keywords expecting it to match but the top results are `README.md` and 5 `__init__` files so they switch to `text-embedding-3-large` and get a bigger disk :rofl: and performance gets **worse**. Convince their boss to pay for Pinecone, :rofl: :rofl: :rofl: still no beans! Still haven't added a keyword search!









# High Level Concepts
# Pitfalls
# Embedding
## Cost
## Size
## Chunking
# The Data