# Introduction to Phase 3

In Phase 1, Nexus searched by exact keywords. In Phase 2, with SQL LIKE patterns. In Phase 3, Nexus searches by meaning. A query for "happy moments" finds a note titled "Great day at the park" even though the words never overlap. Then we go further: Nexus answers natural language questions about your notes using RAG. This is where your project becomes genuinely impressive.

# Days 39–43: Embeddings and Vector Search

Learn embeddings, vector databases, and build semantic search. (15 hours)

## Reading: The Limits of Keyword Search

Keyword search finds exact text. Searching "automobile" won’t find "car." Searching "happy" won’t find "joyful." Computers see text as arbitrary character sequences with no understanding of meaning. Embeddings solve this.

## Reading: What Are Embeddings?

An embedding is a list of numbers (a vector) that captures text meaning. An embedding model (neural network) converts any text into this representation. The key property: similar meanings produce similar vectors.

Imagine a map where every concept has coordinates. "King" and "Queen" are close. "King" and "Apple" are far apart. Embeddings are like coordinates, but in hundreds of dimensions instead of two.

### Cosine Similarity

Measures the angle between vectors. Same direction = 1 (identical meaning). Perpendicular = 0 (unrelated). Opposite = -1.

```python
import numpy as np
def cosine_similarity(a, b):
    return np.dot(a, b) / (np.linalg.norm(a) * np.linalg.norm(b))

# Similar texts produce high similarity:
sim(embed("car"), embed("automobile"))  # ~0.92
sim(embed("car"), embed("banana"))       # ~0.15
```

### Embedding Models

Cloud: OpenAI text-embedding-3-small (high quality, costs per token). Local: sentence-transformers/all-MiniLM-L6-v2 (free, runs on your machine). We use local for learning.

```python
from sentence_transformers import SentenceTransformer
model = SentenceTransformer("all-MiniLM-L6-v2")
vectors = model.encode(["The cat sat on the mat",
                         "A kitten rested on the rug"])
# vectors.shape = (2, 384) — 384 dimensions per vector
```

> **💡 Dimensionality**
>
> Each embedding is 384–3072 numbers depending on the model. More dimensions = more nuance but more storage. all-MiniLM-L6-v2 uses 384 — a good balance for learning.

## Reading: Vector Databases

Regular databases use B-tree indexes for exact matches. Vector databases use ANN (Approximate Nearest Neighbor) indexes for similarity search. They find the K most similar vectors in milliseconds across millions of records. We use ChromaDB.

## Build: Semantic Search for Nexus

```python
pip install chromadb sentence-transformers
# vector_store.py
import chromadb
from sentence_transformers import SentenceTransformer

class VectorStore:
    def __init__(self):
        self.client = chromadb.PersistentClient(path="./chroma_db")
        self.collection = self.client.get_or_create_collection(
            name="nexus_notes",
            metadata={"hnsw:space": "cosine"}
        )
        self.model = SentenceTransformer("all-MiniLM-L6-v2")

    def add_note(self, note_id: str, text: str, metadata: dict):
        embedding = self.model.encode(text).tolist()
        self.collection.upsert(
            ids=[note_id], embeddings=[embedding],
            documents=[text], metadatas=[metadata]
        )

    def search(self, query: str, n_results: int = 5):
        q_emb = self.model.encode(query).tolist()
        return self.collection.query(
            query_embeddings=[q_emb], n_results=n_results
        )
```

Add /notes/search/semantic endpoint. When notes are created, index them in ChromaDB alongside PostgreSQL.

## Review: Days 39–43

**Daily Checklist**

- [ ] I understand embeddings as meaning-preserving vectors
- [ ] I know cosine similarity and what scores mean
- [ ] I can generate embeddings with sentence-transformers
- [ ] I understand vector DBs vs traditional DBs
- [ ] I can use ChromaDB for semantic search
- [ ] Nexus finds notes by meaning, not just keywords

> **🧠 Quiz**
>
> **Q1: What is an embedding?**
> A) Compressed file
> B) Numerical vector representing text meaning
> C) Database index
> D) Encryption type
> ✅ Answer: B
>
> **Q2: Cosine similarity of 0.95 means?**
> A) Unrelated
> B) Nearly identical meaning
> C) Computation error
> D) Opposite meaning
> ✅ Answer: B
>

# Days 44–48: RAG — Chat With Your Notes

Combine search + AI generation. Nexus answers questions about your data. (15 hours)

## Reading: What Is RAG?

RAG (Retrieval-Augmented Generation) has three steps:

1. Retrieve: Convert the question to an embedding, search vector DB for relevant notes.

2. Augment: Include retrieved notes as context in an LLM prompt.

3. Generate: The LLM reads context and generates an answer grounded in YOUR data.

The key insight: LLMs know nothing about your notes. By injecting relevant notes into the prompt, you ground the model. Without this, LLMs hallucinate — generating confident but factually wrong answers.

## Reading: Calling LLM APIs

```python
from openai import OpenAI
client = OpenAI()  # Uses OPENAI_API_KEY env var

response = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "What is Python?"}
    ]
)
answer = response.choices[0].message.content
```

## Reading: Prompt Engineering for RAG

```python
SYSTEM_PROMPT = """You are Nexus, an AI that answers questions
based ONLY on the user's notes below.
Rules:
1. Only use information from provided notes.
2. If notes don't have the answer, say so honestly.
3. Cite which note(s) you used.
4. Be concise.

User's relevant notes:
{context}"""
```

> **💡 Context Window**
>
> LLMs have max input sizes (128K tokens for GPT-4o-mini). You cannot dump all notes in — retrieve only the most relevant. This is why the Retrieve step is critical.

## Build: Nexus Chat

```sql
class NexusRAG:
    def __init__(self):
        self.vs = VectorStore()
        self.llm = OpenAI()

    def chat(self, question: str, n_context: int = 5):
        # 1. Retrieve
        results = self.vs.search(question, n_results=n_context)
        notes = results["documents"][0]

        # 2. Augment
        context = "\n---\n".join(
            f"Note {i+1}: {doc}" for i, doc in enumerate(notes))
        system = SYSTEM_PROMPT.format(context=context)

        # 3. Generate
        resp = self.llm.chat.completions.create(
            model="gpt-4o-mini",
            messages=[{"role":"system","content":system},
                      {"role":"user","content":question}]
        )
        return {"answer": resp.choices[0].message.content,
                "sources": notes}
```

## Review: Days 44–48

**Daily Checklist**

- [ ] I understand RAG: Retrieve → Augment → Generate
- [ ] I can call LLM APIs from Python
- [ ] I understand prompt engineering and system prompts
- [ ] I know what hallucination is and how RAG reduces it
- [ ] Nexus has /chat for Q&A over personal notes

> **🧠 Quiz**
>
> **Q1: What does RAG stand for?**
> A) Recursive Automated Generation
> B) Retrieval-Augmented Generation
> C) Real-time AI Gateway
> D) Relational Aggregate Query
> ✅ Answer: B
>
> **Q2: What is hallucination?**
> A) Database error
> B) LLM generating confident but wrong answers
> C) Embedding type
> D) Network timeout
> ✅ Answer: B
>

# Days 49–52: Advanced RAG Techniques

Improve quality with chunking, hybrid search, re-ranking, evaluation, streaming. (12 hours)

## Reading: Chunking Strategies

Long documents should be split for better embedding quality. Strategies:

Fixed-size: split every N tokens. Simple but may cut mid-sentence.

Sentence-based: split on sentence boundaries. Better coherence.

Recursive: try large chunks first, split further if too long. Best balance.

Always use overlap (~50 chars) between chunks so context is not lost at boundaries.

## Reading: Hybrid Search

Semantic search finds meaning. Keyword search (BM25) finds exact terms. Combine both:

```python
def hybrid_search(query, alpha=0.7):
    semantic = vector_store.search(query)   # meaning
    keyword = bm25_search(query)             # exact terms
    return merge_and_rerank(semantic, keyword, alpha)
# alpha: 1.0 = all semantic, 0.0 = all keyword
```

## Reading: Re-Ranking

Initial retrieval is fast but approximate. A re-ranker (cross-encoder model) re-scores top results with higher accuracy. Two-stage: fast retrieval → accurate re-ranking.

## Reading: Streaming Responses

```python
from fastapi.responses import StreamingResponse

async def stream_chat(question):
    context = retrieve_context(question)
    stream = client.chat.completions.create(
        model="gpt-4o-mini", messages=[...], stream=True)
    for chunk in stream:
        if chunk.choices[0].delta.content:
            yield chunk.choices[0].delta.content

@app.post("/chat/stream")
async def chat_stream(question: str):
    return StreamingResponse(stream_chat(question),
        media_type="text/event-stream")
```

## Reading: Evaluating RAG

Metrics: Faithfulness (only uses retrieved context?), Answer Relevance (addresses the question?), Context Relevance (retrieved notes are relevant?). Use ragas or a judge LLM to score.

## Review: Days 49–52

**Daily Checklist**

- [ ] I understand chunking strategies and overlap
- [ ] I can implement hybrid search (keyword + semantic)
- [ ] I understand re-ranking as a two-stage pipeline
- [ ] I can stream LLM responses via SSE
- [ ] I know how to evaluate RAG quality
- [ ] Nexus has production-grade RAG with streaming

> **🧠 Quiz**
>
> **Q1: Why use chunking with overlap?**
> A) Saves storage
> B) Prevents context loss at boundaries
> C) Required by ChromaDB
> D) Faster
> ✅ Answer: B
>
> **Q2: What is hybrid search?**
> A) Two databases
> B) Keyword + semantic search combined
> C) Two collections
> D) SQL + NoSQL
> ✅ Answer: B
>

# Phase 3 Complete: What You Have Built

**Daily Checklist**

- [ ] Semantic search via embeddings and ChromaDB
- [ ] RAG pipeline: Retrieve → Augment → Generate
- [ ] Natural language Q&A over personal notes
- [ ] Chunking, hybrid search, re-ranking
- [ ] Streaming LLM responses
- [ ] RAG evaluation framework

Semantic search + RAG are the most in-demand AI engineering skills right now. This makes Nexus resume-worthy.

> **📚 Concepts to Deepen on Your Own**
>
> • Transformer architecture and attention mechanism
> • Tokenization: BPE, WordPiece, SentencePiece
> • Fine-tuning embedding models on domain data
> • HNSW algorithm for approximate nearest neighbor search
> • Quantization for reducing model size
> • Multi-modal embeddings (text + images)
> • Agentic RAG: LLM decides when to search vs answer
> • HyDE (Hypothetical Document Embeddings) for query expansion
> • RAGAS, TruLens, LangSmith for evaluation
> • Open-source LLMs with Ollama (Llama, Mistral)
> • Function calling and structured LLM output
> • Metadata filtering in retrieval (date, tags)
