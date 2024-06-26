---
draft: true
date: 2024-05-20
authors:
  - me
categories:
  - GenAI
---

# Advanced RAG - Long Context Retrieval
Something I've learned since the [last post](../advanced-rag.md), one RAG does not fit all.

The focus for this post will be on Long Context RAG, coined in late 2023 as 'Needle in a Haystack[^1]' by [Greg Kamradt](https://x.com/GregKamradt). The idea is you've got a large quantity of data, and you seed that data with a known fact, then try to retrieve it. Doing this reliably is not trivial, even for today's most advanced models. In addition, the analysis can be costly.(1) Some have expanded on Greg's work with Multi-Needle experiments.[^2] It's not difficult to imagine why having this capability is valuable. Corporations today rely on many hands to make light work of massive document analysis. Hence my efforts outlined here.
{ .annotate }

1.  ![Cost](../../assets/posts/advanced-rag-continued/cost-dark.png#only-dark)
    ![Cost](../../assets/posts/advanced-rag-continued/cost-light.png#only-light)

<!-- more -->

[^1]: The first instance of "needle in a haystack" analysis: [here](https://x.com/GregKamradt/status/1722386725635580292)
[^2]: Work from [Lance Martin](https://twitter.com/RLanceMartin) using LangChain to perform [Multi Needle in a Haystack](https://github.com/gkamradt/LLMTest_NeedleInAHaystack/blob/main/viz/CreateVisFromLangSmithTesting.ipynb)

## My Needle in a Haystack
First things first, we need something long. Tolkien wrote the greatest book of all time, The Lord of the Rings. Part 1 of his epic will serve my purposes well. The Fellowship of the Ring, weighing in at `261,224` tokens:

```python
import tiktoken

def get_token_count(string: str) -> int:
    """Returns the number of tokens in a text string."""

    encoding = tiktoken.get_encoding("cl100k_base")
    num_tokens = len(encoding.encode(string))
    return num_tokens

print(get_token_count(text))

# 261224
```

Kaggle has the entire trilogy available as a dataset [here](https://www.kaggle.com/datasets/ashishsinhaiitr/lord-of-the-rings-text?select=01+-+The+Fellowship+Of+The+Ring.txt).

### The Fellowship of the Ring
There are two things I need to prepare:

1. Add facts to the text
2. Convert to a PDF

I want a PDF because accurately citing where answers are found is important. PDFs have pages, and I intend to capture the page number and return it to the user.

#### Injecting the Facts
I added the following facts in random spots throughout the text:

```
Tony's favorite color is green.
Bridgette's favorite color is green.
Theo's favorite color is red.
Ruthie's favorite color is pink.
```

Here, buried on page 155. My favorite color... At the top of Sam's mind as they approach Weathertop.

>  The hobbits looked anxiously at the distant hills. Sam looked up into the pale sky, fearing to see hawks or eagles hovering over them with bright unfriendly eyes. 'You do make me feel uncomfortable and lonesome, Strider!' he said.  

>  'What do you advise us to do?' asked Frodo.  

>  'I think,' answered Strider slowly, as if he was not quite sure,
'I think the best thing is to go as straight eastward from here as we
can, to make for the line of hills, not for Weathertop. There we can
strike a path I know that runs at their feet; it will bring us to
Weathertop from the north and less openly. Then we shall see what we
shall see.'  

> Tony's favorite color is green.  

>  All that day they plodded along, until the cold and early evening
came down. The land became drier and more barren; but mists and
vapours lay behind them on the marshes. A few melancholy birds were
piping and wailing, until the round red sun sank slowly into the
western shadows; then an empty silence fell. The hobbits thought of
the soft light of sunset glancing through the cheerful windows of Bag
End far away.

asdfasdf




# Continue

Explicitly searching for a keyword, and gathering the surrounding context to help OpenAI
```python
import tiktoken
import pymupdf
from openai import OpenAI
from rich import print
import re


def get_text_from_pdf(pdf_path: str) -> str:
    """Extracts text from a PDF file."""

    text = ""
    with pymupdf.open(pdf_path) as pdf:
        for page in pdf:
            text += page.get_text()
    return text


def get_token_count(string: str) -> int:
    """Returns the number of tokens in a text string."""

    encoding = tiktoken.get_encoding("cl100k_base")
    num_tokens = len(encoding.encode(string))
    return num_tokens


def shorten_context(context: str, max_tokens: int) -> list:
    """returns a list of strings that are shorter than the max_tokens"""
    
    encoding = tiktoken.get_encoding("cl100k_base")
    tokens = encoding.encode(context)
    token_count = 0
    shortened_contexts = []
    current_context = ""

    for token in tokens:
        token_count += 1
        if token_count >= max_tokens:
            shortened_contexts.append(current_context)
            current_context = ""
            token_count = 0
        current_context += encoding.decode(token)

    if current_context:
        shortened_contexts.append(current_context)

    return shortened_contexts


def get_surrounding_words(text, target, num_words=5):
    # Find all words in the text
    words = re.findall(r'\w+', text)
    
    # Find the position of the target word
    target_words = re.findall(r'\w+', target)
    target_len = len(target_words)
    
    for i in range(len(words)):
        if words[i:i + target_len] == target_words:
            start = max(i - num_words, 0)
            end = min(i + target_len + num_words, len(words))
            return ' '.join(words[start:end])
    
    return None




def call_openai(prompt, json=True):

    client = OpenAI()
    if json:
        response_format = {"type": "json_object"}
        message_content = f"{prompt} Respond with json"
    else:
        response_format = {"type": "text"}
        message_content = prompt

    response = client.chat.completions.create(
        model="gpt-4o",
        response_format=response_format,
        messages=[
            {"role": "user", "content": message_content},
        ]
    )
    return response

text = get_text_from_pdf("./data/the-fellowship.pdf")

# contexts = shorten_context(text, 100000)

# for context in contexts:
#     prompt = f"""
#         Carefully read the included text in it's entirety and answer the question "What is Tony's favorite color?" If you don't find the answer, simply respond with "not found" {context}
#         """

#     response = call_openai(prompt, json=False)
#     print(response.choices[0].message.content)

# Using basic text search and surrounding context


people = [
    "Bridgette",
    "Tony",
    "Theo",
    "Ruthie"
]
text = get_text_from_pdf("./data/the-fellowship.pdf")

for person in people:

    context = get_surrounding_words(text, person, 200)
    prompt = f"""
        Carefully read the included text in it's entirety and answer the question "What is {person} favorite color?" If you don't find the answer, simply respond with "not found" {context}
        """
    response = call_openai(prompt, json=False)
    print(response.choices[0].message.content)


# Results
(venv) tlofreso@MacBook-Pro:~/Projects/learn-llama-index/long-context-rag$ python scratch.py 
Bridgette's favorite color is Green.
Tony's favorite color is green.
Theo’s favorite color is red.
Ruthie's favorite color is pink.
(venv) tlofreso@MacBook-Pro:~/Projects/learn-llama-index/long-context-rag$ 
```

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


A note about GenAI / LLMs breaking the barrier between tech folks and business folks. 