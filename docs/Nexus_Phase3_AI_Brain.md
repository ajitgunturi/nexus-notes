# Phase 3: AI Brain

> **Phase 3 of 4** | Duration: 14 days (~42 hours) | Prerequisites: Phase 2 complete

In Phase 1, Nexus searched by exact keywords. In Phase 2, with SQL LIKE patterns. In Phase 3, Nexus searches by meaning. A query for "happy moments" finds a note titled "Great day at the park" even though the words never overlap. Then we go further: Nexus answers natural language questions about your notes using RAG. This is where your project becomes genuinely impressive.

Each session follows: Reading → Build → Review (checklist + quiz) → Exercises → Further Reading.

---

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

### Quiz

**Q1: What is an embedding?**

- A) Compressed file
- B) Numerical vector representing text meaning
- C) Database index
- D) Encryption type

<details><summary>Answer</summary>

**B**

</details>

**Q2: Cosine similarity of 0.95 means?**

- A) Unrelated
- B) Nearly identical meaning
- C) Computation error
- D) Opposite meaning

<details><summary>Answer</summary>

**B**

</details>

**Q3: If two texts have a cosine similarity of 0.0, what does that tell you about their relationship?**

- A) They are identical
- B) Their meaning vectors are perpendicular — semantically unrelated
- C) One is the negation of the other
- D) The embedding model failed to process them

<details><summary>Answer</summary>

**B** — Cosine similarity measures the angle between vectors. A score of 0.0 means the vectors point in perpendicular directions — the texts share no directional relationship in the embedding space, indicating unrelated meaning. A score of 1.0 is identical direction (same meaning), and -1.0 is opposite direction (opposite meaning).

</details>

**Q4: What does ChromaDB store alongside the raw document text?**

- A) The original file path and modification date only
- B) The embedding vector, the document text, and optional metadata
- C) A compressed binary of the source document
- D) Only the embedding vector — text must be stored separately

<details><summary>Answer</summary>

**B** — ChromaDB stores the embedding vector (for similarity search), the original document string (returned in results), and an optional metadata dictionary (for filtering by tags, dates, etc.). This lets you retrieve both the vector-matched result and the human-readable text in one query.

</details>

## Exercises

**Exercise 1 (Easy): Compare cosine similarity between sentence pairs**

Write a Python script that uses `sentence-transformers` to encode the following sentence pairs and prints the cosine similarity for each. Observe how the scores reflect semantic closeness.

```python
pairs = [
    ("The cat sat on the mat", "A kitten rested on the rug"),
    ("I love programming in Python", "Python is my favorite language"),
    ("The stock market crashed today", "I enjoy hiking in the mountains"),
    ("Machine learning is fascinating", "Deep learning is a subset of ML"),
]
```

<details><summary>Solution</summary>

```python
import numpy as np
from sentence_transformers import SentenceTransformer

model = SentenceTransformer("all-MiniLM-L6-v2")

pairs = [
    ("The cat sat on the mat", "A kitten rested on the rug"),
    ("I love programming in Python", "Python is my favorite language"),
    ("The stock market crashed today", "I enjoy hiking in the mountains"),
    ("Machine learning is fascinating", "Deep learning is a subset of ML"),
]

def cosine_similarity(a: np.ndarray, b: np.ndarray) -> float:
    return float(np.dot(a, b) / (np.linalg.norm(a) * np.linalg.norm(b)))

print(f"{'Pair':<6} {'Similarity':>10}  Sentence A")
print("-" * 70)
for i, (a, b) in enumerate(pairs):
    va, vb = model.encode([a, b])
    sim = cosine_similarity(va, vb)
    print(f"  {i+1}    {sim:>10.4f}  '{a[:40]}'")
    print(f"              {'':>10}  '{b[:40]}'")
    print()
```

Expected: pairs 1, 2, and 4 should score above 0.7; pair 3 (stock market vs hiking) should score below 0.2. This demonstrates that the model captures semantic equivalence, not just shared words.

</details>

---

**Exercise 2 (Medium): Batch search with top-K and score threshold**

Extend the `VectorStore` class from the Build section to add a `search_with_threshold(query, n_results, min_score)` method. It should return only results where the cosine similarity exceeds `min_score`. Test it with `min_score=0.5` on a collection of 10 notes covering 3 different topics.

<details><summary>Solution</summary>

```python
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
        embedding = self.model.encode(text, normalize_embeddings=True).tolist()
        self.collection.upsert(
            ids=[note_id], embeddings=[embedding],
            documents=[text], metadatas=[metadata]
        )

    def search(self, query: str, n_results: int = 5):
        q_emb = self.model.encode(query, normalize_embeddings=True).tolist()
        return self.collection.query(
            query_embeddings=[q_emb], n_results=n_results
        )

    def search_with_threshold(self, query: str, n_results: int = 10,
                               min_score: float = 0.5) -> list[dict]:
        """Return results with cosine similarity >= min_score."""
        q_emb = self.model.encode(query, normalize_embeddings=True).tolist()
        raw = self.collection.query(
            query_embeddings=[q_emb],
            n_results=n_results,
            include=["documents", "distances", "metadatas"]
        )
        results = []
        for doc, dist, meta in zip(
            raw["documents"][0],
            raw["distances"][0],
            raw["metadatas"][0]
        ):
            # ChromaDB cosine space returns distance (0=identical); convert to similarity
            similarity = 1.0 - dist
            if similarity >= min_score:
                results.append({
                    "text": doc,
                    "similarity": round(similarity, 4),
                    "metadata": meta
                })
        return results


# Test
store = VectorStore()
notes = [
    ("n1", "I went for a 5km run this morning", {"topic": "fitness"}),
    ("n2", "Hit the gym for leg day", {"topic": "fitness"}),
    ("n3", "Tried a new pasta recipe for dinner", {"topic": "food"}),
    ("n4", "Deployed the FastAPI app to production", {"topic": "coding"}),
    ("n5", "Debugging a race condition in async code", {"topic": "coding"}),
    ("n6", "Read about HNSW algorithm for vector search", {"topic": "coding"}),
    ("n7", "Went hiking in the national park", {"topic": "fitness"}),
    ("n8", "Made homemade sourdough bread", {"topic": "food"}),
    ("n9", "Reviewed pull request for the auth module", {"topic": "coding"}),
    ("n10", "Meal prep for the week — salads and grains", {"topic": "food"}),
]
for nid, text, meta in notes:
    store.add_note(nid, text, meta)

results = store.search_with_threshold("exercise and physical activity", min_score=0.5)
print(f"Results above 0.5 threshold: {len(results)}")
for r in results:
    print(f"  {r['similarity']:.4f}  {r['text']}")
```

Using `normalize_embeddings=True` means the dot product equals cosine similarity, avoiding an extra division. The top results should be the running, gym, and hiking notes.

</details>

---

**Exercise 3 (Challenge): Persistent Dual-Index — ChromaDB + PostgreSQL**

Extend the `VectorStore` class so that: (1) `add_note` inserts the note into a PostgreSQL `notes` table AND upserts into ChromaDB; (2) `search` queries ChromaDB for the top 5 note IDs, then fetches the full note rows from PostgreSQL using those IDs; (3) a `delete_note(note_id)` method removes it from both stores. Write a short test that adds 3 notes, searches, deletes one, then searches again to confirm it is gone.

<details><summary>Solution</summary>

```python
import psycopg2
import chromadb
from sentence_transformers import SentenceTransformer

class VectorStore:
    def __init__(self, pg_dsn: str):
        self.conn = psycopg2.connect(pg_dsn)
        self._ensure_table()
        self.chroma = chromadb.PersistentClient(path="./chroma_db")
        self.col = self.chroma.get_or_create_collection(
            name="nexus_notes", metadata={"hnsw:space": "cosine"}
        )
        self.model = SentenceTransformer("all-MiniLM-L6-v2")

    def _ensure_table(self):
        with self.conn.cursor() as cur:
            cur.execute("""
                CREATE TABLE IF NOT EXISTS notes (
                    id TEXT PRIMARY KEY,
                    content TEXT NOT NULL,
                    tags TEXT[],
                    created_at TIMESTAMPTZ DEFAULT NOW()
                )
            """)
        self.conn.commit()

    def add_note(self, note_id: str, content: str, tags: list[str] = None):
        with self.conn.cursor() as cur:
            cur.execute(
                "INSERT INTO notes (id, content, tags) VALUES (%s, %s, %s) "
                "ON CONFLICT (id) DO UPDATE SET content = EXCLUDED.content, tags = EXCLUDED.tags",
                (note_id, content, tags or [])
            )
        self.conn.commit()
        embedding = self.model.encode(content).tolist()
        self.col.upsert(
            ids=[note_id], embeddings=[embedding],
            documents=[content], metadatas=[{"tags": ",".join(tags or [])}]
        )

    def search(self, query: str, n_results: int = 5) -> list[dict]:
        q_emb = self.model.encode(query).tolist()
        chroma_results = self.col.query(query_embeddings=[q_emb], n_results=n_results)
        ids = chroma_results["ids"][0]
        distances = chroma_results["distances"][0]
        if not ids:
            return []
        with self.conn.cursor() as cur:
            cur.execute(
                "SELECT id, content, tags, created_at FROM notes WHERE id = ANY(%s)",
                (ids,)
            )
            rows = {row[0]: row for row in cur.fetchall()}
        combined = []
        for note_id, dist in zip(ids, distances):
            if note_id in rows:
                _, content, tags, created_at = rows[note_id]
                combined.append({
                    "id": note_id, "content": content, "tags": tags,
                    "created_at": created_at, "similarity": 1 - dist,
                })
        return sorted(combined, key=lambda x: x["similarity"], reverse=True)

    def delete_note(self, note_id: str):
        with self.conn.cursor() as cur:
            cur.execute("DELETE FROM notes WHERE id = %s", (note_id,))
        self.conn.commit()
        self.col.delete(ids=[note_id])


# Test
store = VectorStore("postgresql://localhost/nexus_dev")
store.add_note("n1", "Went for a morning run", ["fitness"])
store.add_note("n2", "Pasta recipe with basil", ["food"])
store.add_note("n3", "Deployed FastAPI service", ["coding"])

print("Search 'exercise':", store.search("exercise", n_results=3))

store.delete_note("n1")
results = store.search("morning run", n_results=3)
ids = [r["id"] for r in results]
assert "n1" not in ids, "Deleted note should not appear"
print("After delete — 'n1' correctly absent. IDs returned:", ids)
```

Key design decision: upsert in both stores makes the operation idempotent — if ChromaDB fails after PostgreSQL succeeds, retrying is safe because both operations overwrite cleanly on conflict. ChromaDB returns cosine distance (0 = identical), so `1 - distance` converts it to similarity for readability.

</details>

## Further Reading

- [Sentence-Transformers: Pretrained Models](https://www.sbert.net/docs/sentence_transformer/pretrained_models.html) — Official library docs covering `encode()`, batch processing, normalisation, and choosing between pretrained models including `all-MiniLM-L6-v2`.
- [ChromaDB: Getting Started](https://docs.trychroma.com/getting-started) — Covers `PersistentClient`, collections, upsert, query, metadata filtering, and the HNSW distance options (`cosine`, `l2`, `ip`).


---

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

```python
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

### Quiz

**Q1: What does RAG stand for?**

- A) Recursive Automated Generation
- B) Retrieval-Augmented Generation
- C) Real-time AI Gateway
- D) Relational Aggregate Query

<details><summary>Answer</summary>

**B**

</details>

**Q2: What is hallucination?**

- A) Database error
- B) LLM generating confident but wrong answers
- C) Embedding type
- D) Network timeout

<details><summary>Answer</summary>

**B**

</details>

**Q3: In a RAG system, what is the purpose of the system prompt?**

- A) To define the database schema
- B) To inject retrieved context and constrain the LLM's behaviour before the user message
- C) To set the API authentication key
- D) To control the embedding model's output dimension

<details><summary>Answer</summary>

**B** — The system prompt is sent as the first message with `role: "system"`. It instructs the LLM how to behave (tone, rules, scope) and is where retrieved context is injected so the model grounds its answer in your data rather than its training weights.

</details>

**Q4: What is the most direct way to reduce hallucination in a RAG pipeline?**

- A) Use a larger embedding model
- B) Increase the LLM temperature setting
- C) Only pass retrieved, verified context to the LLM and instruct it not to answer beyond that context
- D) Store notes in PostgreSQL instead of ChromaDB

<details><summary>Answer</summary>

**C** — Hallucination occurs when the LLM fills gaps in knowledge with plausible-sounding fiction. The most direct mitigation is to (1) retrieve relevant notes and inject them as context, and (2) include an explicit instruction such as "Only use information from the notes below — if the answer is not there, say so." Increasing temperature makes hallucination worse, not better.

</details>

## Exercises

**Exercise 1 (Easy): Call the OpenAI Chat API and inspect the response**

Write a Python script that sends the question `"What are three benefits of drinking water?"` to `gpt-4o-mini` with a system prompt that says `"Answer in exactly three bullet points. Be concise."` Print the answer text, the model name, and the total tokens used (prompt + completion).

<details><summary>Solution</summary>

```python
from openai import OpenAI

client = OpenAI()

response = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[
        {
            "role": "system",
            "content": "Answer in exactly three bullet points. Be concise."
        },
        {
            "role": "user",
            "content": "What are three benefits of drinking water?"
        }
    ]
)

answer = response.choices[0].message.content
model_used = response.model
total_tokens = response.usage.total_tokens
prompt_tokens = response.usage.prompt_tokens
completion_tokens = response.usage.completion_tokens

print(f"Model: {model_used}")
print(f"Tokens — prompt: {prompt_tokens}, completion: {completion_tokens}, total: {total_tokens}")
print(f"\nAnswer:\n{answer}")
```

`response.usage` is an object with `prompt_tokens`, `completion_tokens`, and `total_tokens`. Tracking token usage matters in production because API costs are billed per token.

</details>

---

**Exercise 2 (Medium): Build a single-file RAG pipeline without a vector database**

Using only `sentence-transformers` and `openai`, implement a `SimpleRAG` class that: (1) is initialised with a list of note strings; (2) on `ask(question, top_k=3)` encodes the question, finds the `top_k` most similar notes using cosine similarity (numpy only — no ChromaDB), builds a system prompt with those notes as context, calls `gpt-4o-mini`, and returns `{"answer": ..., "sources": [...]}`. Test it with 6 notes about different topics.

<details><summary>Solution</summary>

```python
import numpy as np
from sentence_transformers import SentenceTransformer
from openai import OpenAI

SYSTEM_TEMPLATE = """You are Nexus, an AI assistant. Answer the user's question
using ONLY the notes provided below. If the answer is not in the notes, say
"I don't have enough information in your notes to answer that."

Notes:
{context}"""

class SimpleRAG:
    def __init__(self, notes: list[str]):
        self.notes = notes
        self.embedder = SentenceTransformer("all-MiniLM-L6-v2")
        self.llm = OpenAI()
        # Pre-encode all notes; normalise so dot product = cosine similarity
        self.note_vecs = self.embedder.encode(notes, normalize_embeddings=True)

    def _retrieve(self, question: str, top_k: int) -> list[tuple[str, float]]:
        q_vec = self.embedder.encode([question], normalize_embeddings=True)[0]
        scores = self.note_vecs @ q_vec
        top_indices = np.argsort(scores)[::-1][:top_k]
        return [(self.notes[i], float(scores[i])) for i in top_indices]

    def ask(self, question: str, top_k: int = 3) -> dict:
        retrieved = self._retrieve(question, top_k)
        context = "\n\n".join(
            f"[Note {i+1}] {text}" for i, (text, _) in enumerate(retrieved)
        )
        system_prompt = SYSTEM_TEMPLATE.format(context=context)
        response = self.llm.chat.completions.create(
            model="gpt-4o-mini",
            messages=[
                {"role": "system", "content": system_prompt},
                {"role": "user", "content": question}
            ]
        )
        return {
            "answer": response.choices[0].message.content,
            "sources": [text for text, _ in retrieved],
            "scores": [score for _, score in retrieved]
        }


# Test
notes = [
    "I started learning Python in January 2025.",
    "Python is great for data science and machine learning.",
    "I run 5km every morning to stay fit.",
    "My fitness goal for 2025 is to run a half-marathon.",
    "I read 'Atomic Habits' and it changed how I approach goals.",
    "Setting small daily habits is key to long-term success.",
]

rag = SimpleRAG(notes)
result = rag.ask("What are my fitness goals and what am I doing to achieve them?")
print("Answer:", result["answer"])
print("\nSources used:")
for i, src in enumerate(result["sources"]):
    print(f"  [{i+1}] {src}")
```

</details>

---

**Exercise 3 (Challenge): Evaluate hallucination with a judge LLM**

Build a `HallucinationChecker` class that takes an `answer` string and the list of `context` strings used to generate it, then asks `gpt-4o-mini` to act as a judge and return a JSON object `{"faithful": true, "score": 0.95, "reason": "..."}`. The judge prompt must instruct the model to output valid JSON only. Parse with `json.loads` and raise a descriptive `ValueError` if parsing fails. Test it with one faithful answer and one hallucinated answer.

<details><summary>Solution</summary>

```python
import json
from openai import OpenAI

JUDGE_PROMPT = """You are a strict factual accuracy judge for a RAG system.

You will be given:
1. CONTEXT: the notes retrieved for a user's question.
2. ANSWER: the AI-generated answer based on those notes.

Your task: determine whether every factual claim in the ANSWER is supported by the CONTEXT.

Respond with ONLY a valid JSON object in this exact format — no markdown, no explanation:
{{"faithful": <true|false>, "score": <float 0.0 to 1.0>, "reason": "<one sentence>"}}

Rules:
- "faithful" is true only if ALL claims are supported by the context.
- "score" is 1.0 for fully faithful, 0.0 for completely fabricated.
- "reason" must be a single sentence explaining your verdict.

CONTEXT:
{context}

ANSWER:
{answer}"""

class HallucinationChecker:
    def __init__(self):
        self.client = OpenAI()

    def check(self, answer: str, context: list[str]) -> dict:
        context_block = "\n\n".join(f"[Note {i+1}] {c}" for i, c in enumerate(context))
        prompt = JUDGE_PROMPT.format(context=context_block, answer=answer)
        response = self.client.chat.completions.create(
            model="gpt-4o-mini",
            messages=[{"role": "user", "content": prompt}],
            temperature=0  # deterministic for evaluation
        )
        raw = response.choices[0].message.content.strip()
        try:
            result = json.loads(raw)
        except json.JSONDecodeError as e:
            raise ValueError(f"Judge returned invalid JSON. Raw output: '{raw}'. Error: {e}")
        required = {"faithful", "score", "reason"}
        missing = required - result.keys()
        if missing:
            raise ValueError(f"Judge JSON missing required keys: {missing}")
        return result


# Test
checker = HallucinationChecker()
context = [
    "I started learning Python in January 2025.",
    "My fitness goal for 2025 is to run a half-marathon.",
]

faithful_answer = (
    "Based on your notes, you started learning Python in January 2025 "
    "and your fitness goal for 2025 is to run a half-marathon."
)
hallucinated_answer = (
    "You started learning Python in January 2025, your fitness goal is a "
    "full marathon, and you have already completed three 10km races."
)

print("Faithful answer check:")
print(json.dumps(checker.check(faithful_answer, context), indent=2))

print("\nHallucinated answer check:")
print(json.dumps(checker.check(hallucinated_answer, context), indent=2))
```

Setting `temperature=0` is critical for evaluation tasks — you want consistent, deterministic judgements. The hallucinated answer introduces "full marathon" and "three 10km races" which are not in the context, so the judge should return `faithful: false`.

</details>

## Further Reading

- [OpenAI Chat Completions API Reference](https://platform.openai.com/docs/api-reference/chat/create) — Official reference for `messages`, `role` types (system/user/assistant), `temperature`, `max_tokens`, `stream`, and `usage` fields. Essential reading before building production RAG.
- [OpenAI Prompt Engineering Guide](https://platform.openai.com/docs/guides/prompt-engineering) — Covers system prompt design, chain-of-thought, few-shot prompting, and techniques for reducing hallucination — all directly applicable to the RAG system prompt patterns used in Nexus.


---

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

### Quiz

**Q1: Why use chunking with overlap?**

- A) Saves storage
- B) Prevents context loss at boundaries
- C) Required by ChromaDB
- D) Faster

<details><summary>Answer</summary>

**B**

</details>

**Q2: What is hybrid search?**

- A) Two databases
- B) Keyword + semantic search combined
- C) Two collections
- D) SQL + NoSQL

<details><summary>Answer</summary>

**B**

</details>

**Q3: Why is chunk overlap used in text chunking strategies?**

- A) It reduces the total number of chunks stored
- B) It ensures that sentences split across a chunk boundary still appear intact in at least one chunk
- C) It is required by the sentence-transformers library
- D) It increases embedding dimensionality

<details><summary>Answer</summary>

**B** — Without overlap, a sentence that falls exactly on a chunk boundary gets split: the first half lands in chunk N and the second half in chunk N+1. Neither chunk contains the full context of that sentence. Overlap (typically 10–20% of chunk size) copies the tail of chunk N into the start of chunk N+1, ensuring boundary-spanning content is fully represented in at least one chunk.

</details>

**Q4: In a two-stage retrieval pipeline, what is the role of the re-ranker?**

- A) To generate the final answer
- B) To replace the vector database
- C) To re-score a small candidate set with a more accurate cross-encoder model after fast initial retrieval
- D) To split documents into chunks before indexing

<details><summary>Answer</summary>

**C** — The first stage uses fast ANN retrieval to pull a candidate set of, say, 20 results in milliseconds. The second stage passes those 20 candidates to a cross-encoder re-ranker, which scores each (query, document) pair together with higher accuracy — because it can attend to both texts simultaneously — and returns a reordered top-K. This trades a small latency increase for significantly better precision.

</details>

## Exercises

**Exercise 1 (Easy): Implement and compare three chunking strategies**

Write three functions: `fixed_chunk(text, size, overlap)`, `sentence_chunk(text, max_sentences)`, and `recursive_chunk(text, max_size, overlap)`. Apply all three to the same 500-word paragraph. Print the number of chunks and the length of each chunk for every strategy. Observe where sentences get cut off in fixed chunking.

<details><summary>Solution</summary>

```python
import re

SAMPLE_TEXT = (
    "Python is a high-level, general-purpose programming language. "
    "Its design philosophy emphasises code readability with the use of significant indentation. "
    "Python is dynamically typed and garbage-collected. It supports multiple programming paradigms, "
    "including structured, object-oriented and functional programming. It is often described as a "
    "batteries included language due to its comprehensive standard library. "
    "Guido van Rossum began working on Python in the late 1980s as a successor to the ABC programming language. "
    "Python 3.0 was released in 2008. Python consistently ranks as one of the most popular programming languages. "
    "It is widely used in web development, data science, machine learning, artificial intelligence, "
    "scientific computing, and automation. "
    "The Python Package Index (PyPI) hosts hundreds of thousands of third-party libraries. "
    "Python uses indentation to delimit code blocks rather than curly braces. "
    "This enforces a uniform coding style and makes programs easier to read. "
    "The language is named after the British comedy group Monty Python, not the snake. "
)

def fixed_chunk(text: str, size: int = 200, overlap: int = 50) -> list[str]:
    chunks = []
    start = 0
    while start < len(text):
        end = start + size
        chunks.append(text[start:end])
        start += size - overlap
    return chunks

def sentence_chunk(text: str, max_sentences: int = 3) -> list[str]:
    sentences = re.split(r'(?<=[.!?])\s+', text.strip())
    chunks = []
    for i in range(0, len(sentences), max_sentences):
        chunks.append(" ".join(sentences[i:i + max_sentences]))
    return chunks

def recursive_chunk(text: str, max_size: int = 200, overlap: int = 50) -> list[str]:
    if len(text) <= max_size:
        return [text]
    sentences = re.split(r'(?<=[.!?])\s+', text.strip())
    if len(sentences) <= 1:
        return fixed_chunk(text, size=max_size, overlap=overlap)
    mid = len(sentences) // 2
    left = " ".join(sentences[:mid])
    right = " ".join(sentences[mid:])
    overlap_text = sentences[mid - 1] if mid > 0 else ""
    right_with_overlap = overlap_text + " " + right if overlap_text else right
    return (recursive_chunk(left, max_size, overlap) +
            recursive_chunk(right_with_overlap, max_size, overlap))

def print_chunks(name: str, chunks: list[str]):
    print(f"\n--- {name} ({len(chunks)} chunks) ---")
    for i, c in enumerate(chunks):
        print(f"  [{i+1}] len={len(c):4d}  '{c[:60]}...'")

print_chunks("Fixed (size=200, overlap=50)",   fixed_chunk(SAMPLE_TEXT, 200, 50))
print_chunks("Sentence (max 3 sentences)",      sentence_chunk(SAMPLE_TEXT, 3))
print_chunks("Recursive (max_size=200)",        recursive_chunk(SAMPLE_TEXT, 200, 50))
```

Observe how fixed chunking cuts mid-sentence while sentence chunking always ends at a full stop. Recursive chunking adapts to natural boundaries and falls back gracefully.

</details>

---

**Exercise 2 (Medium): Implement hybrid search with BM25 + semantic scores**

Install `rank-bm25`. Write a `HybridSearch` class that indexes a list of documents in both BM25 and a sentence-transformers embedding matrix. `search(query, top_k=5, alpha=0.7)` returns results ranked by a weighted combination: `alpha * semantic_score + (1 - alpha) * bm25_score` (both normalised to 0–1). Print results for `alpha=1.0`, `alpha=0.0`, and `alpha=0.6` for the same query and observe where they differ.

<details><summary>Solution</summary>

```python
import numpy as np
from rank_bm25 import BM25Okapi
from sentence_transformers import SentenceTransformer

class HybridSearch:
    def __init__(self, documents: list[str]):
        self.documents = documents
        self.model = SentenceTransformer("all-MiniLM-L6-v2")
        tokenised = [doc.lower().split() for doc in documents]
        self.bm25 = BM25Okapi(tokenised)
        self.sem_matrix = self.model.encode(documents, normalize_embeddings=True)

    def _normalise(self, scores: np.ndarray) -> np.ndarray:
        mn, mx = scores.min(), scores.max()
        if mx - mn < 1e-9:
            return np.zeros_like(scores)
        return (scores - mn) / (mx - mn)

    def search(self, query: str, top_k: int = 5, alpha: float = 0.7) -> list[dict]:
        q_vec = self.model.encode([query], normalize_embeddings=True)[0]
        sem_scores = self.sem_matrix @ q_vec
        bm25_scores = np.array(self.bm25.get_scores(query.lower().split()))
        sem_norm = self._normalise(sem_scores)
        bm25_norm = self._normalise(bm25_scores)
        combined = alpha * sem_norm + (1 - alpha) * bm25_norm
        top_indices = np.argsort(combined)[::-1][:top_k]
        return [
            {
                "text": self.documents[i],
                "combined": float(combined[i]),
                "semantic": float(sem_norm[i]),
                "bm25": float(bm25_norm[i]),
            }
            for i in top_indices
        ]


documents = [
    "Python is a popular programming language for data science.",
    "Running and jogging improve cardiovascular health significantly.",
    "The Python snake is found in Africa and Asia.",   # BM25 trap: "Python" keyword
    "Machine learning uses Python libraries like scikit-learn.",
    "A healthy diet combined with exercise reduces disease risk.",
    "Natural language processing with Python and Hugging Face.",
    "Marathon training requires consistent weekly mileage.",
    "FastAPI is a modern Python web framework.",
]

engine = HybridSearch(documents)
query = "Python for machine learning"

for alpha in [1.0, 0.0, 0.6]:
    label = 'pure semantic' if alpha == 1.0 else 'pure BM25' if alpha == 0.0 else 'hybrid'
    print(f"\nalpha={alpha} ({label}):")
    for r in engine.search(query, top_k=3, alpha=alpha):
        print(f"  sem={r['semantic']:.2f} bm25={r['bm25']:.2f} combined={r['combined']:.2f}  '{r['text'][:55]}'")
```

At `alpha=0.0` (pure BM25) the Python snake note may rank highly because it contains the keyword "Python". At `alpha=1.0` (pure semantic) it ranks low because the meaning is unrelated to machine learning. The hybrid at `alpha=0.6` correctly surfaces the ML-relevant notes.

</details>

---

**Exercise 3 (Challenge): Build a RAG evaluation pipeline using RAGAS metrics**

Using the `ragas` library, evaluate your RAG pipeline on a small hand-crafted test set of 5 question-answer-context triples. Compute `faithfulness` and `answer_relevancy` scores. Then deliberately degrade one answer (inject a hallucinated fact) and show the faithfulness score drops. Install: `pip install ragas datasets`.

<details><summary>Solution</summary>

```python
from datasets import Dataset
from ragas import evaluate
from ragas.metrics import faithfulness, answer_relevancy
from openai import OpenAI
import numpy as np
from sentence_transformers import SentenceTransformer

client = OpenAI()
embedder = SentenceTransformer("all-MiniLM-L6-v2")

NOTES = [
    "I started a Python course on Coursera in January 2025.",
    "My fitness goal for 2025 is to complete a half-marathon in under 2 hours.",
    "I use FastAPI to build REST APIs for my personal projects.",
    "I read 'Deep Work' by Cal Newport and now block 4 hours each morning for focused coding.",
    "I track my running with Garmin; my best 5km time is 24 minutes.",
]
note_vecs = embedder.encode(NOTES, normalize_embeddings=True)

def retrieve(question: str, top_k: int = 2) -> list[str]:
    q_vec = embedder.encode([question], normalize_embeddings=True)[0]
    scores = note_vecs @ q_vec
    return [NOTES[i] for i in np.argsort(scores)[::-1][:top_k]]

def generate_answer(question: str, context: list[str]) -> str:
    ctx = "\n".join(f"- {c}" for c in context)
    resp = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "system",
             "content": f"Answer using ONLY these notes:\n{ctx}\nIf not in notes, say so."},
            {"role": "user", "content": question}
        ],
        temperature=0
    )
    return resp.choices[0].message.content

questions = [
    "When did I start my Python course?",
    "What is my half-marathon goal?",
    "What framework do I use for REST APIs?",
    "How do I protect time for focused work?",
    "What is my best 5km time?",
]
ground_truths = ["January 2025", "Under 2 hours", "FastAPI", "Block 4 hours each morning", "24 minutes"]

rows = {"question": [], "answer": [], "contexts": [], "ground_truth": []}
for q, gt in zip(questions, ground_truths):
    ctx = retrieve(q, top_k=2)
    ans = generate_answer(q, ctx)
    rows["question"].append(q)
    rows["answer"].append(ans)
    rows["contexts"].append(ctx)
    rows["ground_truth"].append(gt)

dataset = Dataset.from_dict(rows)
result = evaluate(dataset, metrics=[faithfulness, answer_relevancy])
print("\n=== RAG Evaluation Results ===")
print(result)

# Inject hallucination in Q5
rows_bad = dict(rows)
rows_bad["answer"] = list(rows["answer"])
rows_bad["answer"][4] = "Your best 5km time is 19 minutes and you have won two local races."
dataset_bad = Dataset.from_dict(rows_bad)
result_bad = evaluate(dataset_bad, metrics=[faithfulness, answer_relevancy])
print("\n=== After Injecting Hallucination (Q5) ===")
print(result_bad)
print("\nFaithfulness delta:", result_bad["faithfulness"] - result["faithfulness"])
```

Expected: faithfulness drops noticeably when the hallucinated answer is included. `answer_relevancy` may remain similar because the hallucinated answer is still topically relevant — demonstrating that faithfulness and relevancy are orthogonal metrics and you need both.

</details>

## Further Reading

- [RAGAS: Evaluation Framework for RAG Pipelines](https://docs.ragas.io/en/latest/getstarted/index.html) — Covers the faithfulness, answer relevancy, context precision, and context recall metrics, with code examples for integrating with LangChain and bare OpenAI clients.
- [Hugging Face: Text Chunking Strategies for RAG](https://huggingface.co/learn/cookbook/en/rag_evaluation) — A practical guide covering fixed-size, sentence-based, and recursive chunking with `langchain.text_splitter`, overlap rationale, and benchmark comparisons of chunk sizes on retrieval quality.


---

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
