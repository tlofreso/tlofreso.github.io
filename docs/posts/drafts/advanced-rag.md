---
draft: true
date: 2024-05-15
authors:
  - me
categories:
  - GenAI
---

# Advanced RAG
_First things first... This is ^^not^^ advanced RAG. But it does mark an evolution in my ability, and feels advanced to 2024 Tony._

The other day, I came across [this post](https://twitter.com/llama_index/status/1788686110593368509). It's a video of [El Risitas](https://en.wikipedia.org/wiki/El_Risitas) guy, and <!-- more -->the subtitles (included below) convey all the _laughably ridiculous_ things people writing RAG apps today are doing.

> They split the file into chunks by page, or by paragraph or who the fuck knows. Embed it with `ada-002` for a couple cents. Store the embeddings in some hacked together database. They match your query by cosine similarity :laughing: and return the top k. :rofl: If the answer is bad ... they just increase the value of k! :laughing: :rofl: Users have no idea how embeddings work, so they fill their query full of keywords expecting it to match but the top results are `README.md` and 5 `__init__` files so they switch to `text-embedding-3-large` and get a bigger disk :rofl: and performance gets **worse**. Convince their boss to pay for Pinecone, :rofl: :rofl: :rofl: still no beans! Still haven't added a keyword search!

![el-risitas](../../assets/posts/advanced-rag/el-risitas.jpeg)

Derisive tactics aside, having someone tell you how not to do something can help inform better approaches. I had confidently "increased the value of k" and "switched to `text-embedding-3-large`". Afterall, OpenAI [says](https://platform.openai.com/docs/assistants/tools/file-search/how-it-works) that's a best practice! Lest we forget:triangular_flag_on_post:... OpenAI will store your embedded data for $0.10/GB. That adds up quick when the default settings are:

 - Model: `text-embedding-3-large`
 - Chunk Size: `800 tokens`
 - Chunk Overlap: `400 tokens`

With all this as a backdrop, here are some of my musings while learning how to do RAG.

## Where to Begin?

For my use case, I have three goals:

1. Ingest custom data
2. Return accurate responses to user prompts
3. Always include a citation in the response

I'll be using [LlamaIndex](https://www.llamaindex.ai/open-source).  

One of the better Getting Started resources I've found is this [High-Level Concepts (RAG)](https://docs.llamaindex.ai/en/stable/getting_started/concepts/) page from the LlamaIndex docs. It covers, in general, the five 'stages' every RAG app will have:

``` mermaid
flowchart LR
    A[Loading] --> B[Indexing] --> C[Storing] --> D[Querying] --> E[Evaluating]
```

What you quickly discover, is each of these stages may have several steps (pipelines), and configuration varies widely. In the case of Querying, there are generally three sub-stages: Retrieval, Post Processing, and Response Synthesis. If we look at Retrieval, LlamaIndex covers 17 different [Retrievers](https://docs.llamaindex.ai/en/stable/api_reference/retrievers/) in their docs alone. Any of these can be used singularly, or combined for various effects and tactics you may want to employ when RAG-ing your data. Daunting, I know. But at least they've given us a framework to break things down.

## Common Pitfalls

There are a few patterns I've noticed that newbies encounter.

1. Users want citations. The first way folks try to accomplish this, is by coaxing the llm to cite the response. "Be sure to include the page where the answer was found." I've found this technique to be wildly inconsistent, and inaccurate.
2. As your dataset increases... querying, and the steps you perform before that stage get much more complex. Early wins feel really great, but quickly dissolve as soon as you begin to scale.
3. OpenAI is really, really fast. The hurdle you solved for this week, may simply not exist next week. This creates a weird dynamic for prioritizing work

## The Five Stages
### Loading

Loading your data is part of the "Ingestion Pipeline" which includes: loading -> transforming -> indexing -> storage. I've been scoping my loading stage to use LlamaIndex's `SimpleDirectoryReader` because it's straightforward to setup, and is general enough for my use case. Here's how we can load some data:

```python
from llama_index.core import (SimpleDirectoryReader)

def load():
    """Stage 1: Load Data"""
    documents = SimpleDirectoryReader("data").load_data()
    return documents

if __name__ == "__main__":
    print(load())
```

My data consists of rule books from GMT war games; [Twilight Struggle](https://www.gmtgames.com/living_rules/TS_Rules_Deluxe.pdf), [Churchill](https://www.gmtgames.com/churchill/CHURCHILLRules-Final.pdf), and [For the People](https://www.gmtgames.com/nnfp/FTP-rules-2014.pdf). The data returned from `SimpleDirectoryReader` is a list of "Documents".

!!! note "LlamaIndex definition of a Document"

    A Document is a generic container around any data source - for instance, a PDF, an API output, or retrieved data from a database. They can be constructed manually, or created automatically via our data loaders.

In our case, the load stage returned a list of 116 documents which happens to equal the total pages for all three rule books. This must be how LlamaIndex handles PDFs when using the `SimpleDirectoryReader`.

```python title="Content of a LlamaIndex document"
Document(
        id_='c7adc363-32e7-42b3-99fe-b356cd2f0be7',
        embedding=None,
        metadata={'page_label': '22', 'file_name': 'CHURCHILLRules-Final.pdf', 'file_path': 'C:\\projects\\learn-llama-index\\advanced-rag\\data\\CHURCHILLRules-Final.pdf', 'file_type': 'application/pdf', 'file_size': 2051633, 'creation_date': '2024-05-12', 'last_modified_date': '2024-05-12'},
        excluded_embed_metadata_keys=['file_name', 'file_type', 'file_size', 'creation_date', 'last_modified_date', 'last_accessed_date'],
        excluded_llm_metadata_keys=['file_name', 'file_type', 'file_size', 'creation_date', 'last_modified_date', 'last_accessed_date'],
        relationships={},
        text='Churchill Rules22\n© 2015 GMT Games, LLC\n10.0 Secret Agenda Variant \nDESIGN NOTE: I have found gamers of two minds on games \nwith continuous scoring like Churchill. Some folks like know-ing the exact score at all times and manage their decisions based on perfect ‘score’ information, whileothers like a bit more uncertainty. The core game uses perfect scoring but for those who like to have a bit more bluff in their games I offer the following official variant.\n10.1 Secret Agenda Markers\n ‘Secret Agenda’ markers are marked as such and also have the name of a country or colony on one side. Note that there are some duplicates for some countries/colonies; this is intentional and these are not extras.\n10.2 Secret Agenda Procedure\nTake the 36 Secret Agenda markers; each player secretly and randomly draws three markers. Do not show them to your op-ponents. At the end of the game before you determine the winner, all players reveal their Secret Agenda markers.10.3 Secret Agenda Scoring\nIf at the end of the game a player has a Political Alignment marker in a country/colony that matches one of their Secret Agenda markers they score five additional points per marker, for a potential of 15 points if all three markers meet this condition. After these points have been applied to each players score the winner is determined.\nPLAY NOTE: It is possible and intentional that the application of the Secret Agenda marker bonus could impact the condition under which the winner is determined, i.e.,    creates a 15 point difference in score that can change the winner. This fact needs to be incorporated into a player’ s strategy, so be careful how hard you fight for your Secret Agenda.\nPLAY NOTE: You score 5 VP per Secret Agenda marker, so if you have two markers for the same location, you would score 10 VP .\nStalin and Churchill enjoy a private moment during the Yalta Conference.',
        start_char_idx=None,
        end_char_idx=None,
        text_template='{metadata_str}\n\n{content}',
        metadata_template='{key}: {value}',
        metadata_seperator='\n'
)
```

The metadata returned here is pretty handy. My hope is that I'll be able to use this to accurately cite answers later.

### Indexing
Indexing typically involves generating embeddings. An [Embedding](https://platform.openai.com/docs/guides/embeddings/what-are-embeddings) "is a vector (list) of floating point numbers. The distance between two vectors measures their relatedness." Send text in, get a load of floats back. Here's an example.  

_Note. I reduced the `dimensions` significantly to 30. By default, thousands of floats would be returned._
```bash
tlofreso@MacBook-Pro:~$ curl https://api.openai.com/v1/embeddings \
>   -H "Content-Type: application/json" \
>   -H "Authorization: Bearer $OPENAI_API_KEY" \
>   -d '{
>     "input": "Once more unto the breach, dear friends, once more",
>     "model": "text-embedding-3-small",
>     "dimensions": 30
>   }'
{
  "object": "list",
  "data": [
    {
      "object": "embedding",
      "index": 0,
      "embedding": [
        0.14859965,
        0.25593004,
        0.1891131,
        0.10665011,
        0.08737599,
        0.0665146,
        0.016647546,
        0.15918152,
        -0.25834873,
        -0.018678887,
        0.20302069,
        -0.23854554,
        -0.12834294,
        -0.18593854,
        0.21073033,
        0.09992307,
        -0.20029964,
        0.08027103,
        -0.13333154,
        0.2108815,
        -0.23340577,
        0.13514556,
        0.123429924,
        0.012348661,
        -0.26983762,
        0.1795894,
        -0.10135917,
        -0.0073128263,
        0.48374254,
        -0.10302204
      ]
    }
  ],
  "model": "text-embedding-3-small",
  "usage": {
    "prompt_tokens": 11,
    "total_tokens": 11
  }
}
tlofreso@MacBook-Pro:~$ 
```

By default LlamaIndex will use the `text-embedding-ada-002` model for embedding. I switched the embedding model to the new `text-embedding-3-small` because it's [more capable](https://platform.openai.com/docs/guides/embeddings/embedding-models), and 1/5th of the price. Changing the default chunk size, and overlap has a significant effect on the embeddings. OpenAI is using the `text-embedding-3-large` model, with chunk sizes of 800 and 50% overlap. While `text-embedding-3-large` is a more capable model, it takes up significantly more disk. For three files totaling `7.04mb` this is what you'll end up with for the various models and chunk configurations:

#### text-embedding-3-small
```
Default settings:                   5 files - 7.75mb
chunk_size=1000, chunk_overlap=200: 5 files - 9.55mb
chunk_size=800, chunk_overlap=400:  5 files - 11.5mb
chunk_size=200, chunk_overlap=100:  5 files - 64.9mb
```

#### text-embedding-3-large
```
Default settings:                   5 files - 14.3mb
chunk_size=1000, chunk_overlap=200: 5 files - 17.9mb
chunk_size=800, chunk_overlap=400:  5 files - 21.5mb
chunk_size=200, chunk_overlap=100:  5 files - 124mb
```

#### text-embedding-ada-002
```
Default settings:                   5 files - 7.78mb
chunk_size=1000, chunk_overlap=200: 5 files - 9.61mb
chunk_size=800, chunk_overlap=400:  5 files - 11.6mb
chunk_size=200, chunk_overlap=100:  5 files - 65.3mb
```

Here are the settings I ended up having the most success with:
```python
Settings.llm = OpenAI(model="gpt-4o")
Settings.embed_model = OpenAIEmbedding(model="text-embedding-3-small")
Settings.text_splitter = SentenceSplitter(chunk_size=1000, chunk_overlap=200)
```

### Storage
For now, we're keeping storage simple. Right on the local filesystem. This code will...

1. Set the llm, embedding, and chunk config
2. Load the data
3. Generate the embeddings
4. Store those embeddings on the filesystem
5. Checks for existing embeddings, creates them if missing

```python
import os
from llama_index.core import (Settings, SimpleDirectoryReader, VectorStoreIndex, StorageContext)

Settings.llm = OpenAI(model="gpt-4o")
Settings.embed_model = OpenAIEmbedding(model="text-embedding-3-small")
Settings.text_splitter = SentenceSplitter(chunk_size=1000, chunk_overlap=200)
PERSIST_DIR = "./storage"

def load():
    """Stage 1: Load Data"""
    documents = SimpleDirectoryReader("data").load_data()
    return documents

def index(documents):
    """Stage 2: Index Data"""
    index = VectorStoreIndex.from_documents(documents, show_progress=True)
    index.storage_context.persist(persist_dir=PERSIST_DIR)


if __name__ == "__main__":

    # See if the index has been created, and create it if it hasn't
    if not os.path.exists(PERSIST_DIR):
        index(load())
    
```

### Querying

### Evaluating

https://www.eyelevel.ai/post/most-accurate-rag
> Cosine similarity is a popular strategy, but it has a big limitation; it’s often tripped up by semantic similarity. Basically, cosine similarity can get confused if there are superficial similarities in two pieces of text. Because many of the documents contain information about tax information, it’s likely that LangChain and LlamaIndex both got confused and simply returned a chunk which was irrelevant to the prompt.