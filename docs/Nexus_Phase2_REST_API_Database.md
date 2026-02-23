# Phase 2: REST API and Database

> **Phase 2 of 4** | Duration: 20 days (~60 hours) | Prerequisites: Phase 1 complete

In Phase 1, Nexus was a CLI tool that only you could use, on your machine. In Phase 2, it becomes a web service that any program or device on the network can talk to. You will learn the protocols that power the internet (HTTP), the language of databases (SQL), the art of async programming, and how to package everything in Docker containers.

By the end of Phase 2, Nexus will be a multi-user REST API backed by PostgreSQL, running in Docker, with JWT authentication and proper security.

Each session follows the same pattern: Reading (concept + code), Build (add it to Nexus), Review (checklist + quiz), Exercises, and Further Reading. Do not skip the quizzes.

---

# Days 19–21: HTTP and Web Fundamentals

You learn what happens when you open a website and build your first web API. (9 hours)

## Reading: How the Web Works

When you type a URL in your browser: (1) your browser sends an HTTP request to a server, (2) the server processes it, (3) the server sends back an HTTP response. HTTP (HyperText Transfer Protocol) is the language browsers and servers speak.

An HTTP request has four parts: a method (what action), a URL (what resource), headers (metadata like authentication tokens), and optionally a body (data being sent).

| Method | Purpose                        | Example         |
| ------ | ------------------------------ | --------------- |
| GET    | Retrieve data (read-only)      | GET /notes      |
| POST   | Create new data                | POST /notes     |
| PUT    | Replace existing data entirely | PUT /notes/1    |
| PATCH  | Update part of data            | PATCH /notes/1  |
| DELETE | Remove data                    | DELETE /notes/1 |

### REST: Representational State Transfer

REST is a set of conventions for designing web APIs. Each URL represents a resource (like a note), and HTTP methods define the action. A RESTful API is stateless — each request is self-contained; the server does not remember previous requests.

### Status Codes

| Code | Meaning               | When Used                      |
| ---- | --------------------- | ------------------------------ |
| 200  | OK                    | Successful GET, PUT, PATCH     |
| 201  | Created               | Successful POST (new resource) |
| 204  | No Content            | Successful DELETE              |
| 400  | Bad Request           | Invalid input from client      |
| 401  | Unauthorized          | Missing/invalid authentication |
| 403  | Forbidden             | Authenticated but not allowed  |
| 404  | Not Found             | Resource does not exist        |
| 422  | Unprocessable Entity  | Validation error               |
| 500  | Internal Server Error | Server crashed                 |

> **💡 The Pattern**
>
> 2xx = success, 3xx = redirect, 4xx = client error (YOU sent something wrong), 5xx = server error (the SERVER broke). Memorize 200, 201, 400, 401, 404, 500.

## Reading: JSON as the API Language

REST APIs use JSON to send and receive data. You already know JSON from Phase 1 — now it becomes the communication format between machines.

## Reading: FastAPI

FastAPI is a modern Python web framework. It is fast (built on async), has automatic documentation, and uses Python type hints for request validation. Install:

```bash
pip install fastapi uvicorn
```

### What Is Uvicorn?

FastAPI defines your application. Uvicorn is the ASGI server that actually runs it and handles incoming HTTP connections. Think of FastAPI as the recipe and Uvicorn as the chef.

## Build: Nexus API

```python
# api.py
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import Optional
from nexus.store import NoteStore

app = FastAPI(title="Nexus API", version="2.0.0")
store = NoteStore()

class NoteCreate(BaseModel):
    title: str
    content: str = ""
    tags: list[str] = []

class NoteResponse(BaseModel):
    title: str
    content: str
    tags: list[str]
    created_at: str

@app.get("/notes")
def list_notes(search: Optional[str] = None):
    if search:
        return [n.to_dict() for n in store.search(search)]
    return [n.to_dict() for n in store.notes]

@app.post("/notes", status_code=201)
def create_note(note: NoteCreate):
    created = store.add(note.title, note.content, note.tags)
    return created.to_dict()

@app.get("/notes/{index}")
def get_note(index: int):
    if 0 <= index < len(store.notes):
        return store.notes[index].to_dict()
    raise HTTPException(status_code=404, detail="Not found")

@app.delete("/notes/{index}")
def delete_note(index: int):
    try:
        store.delete(index)
        return {"message": "Deleted"}
    except IndexError:
        raise HTTPException(status_code=404, detail="Not found")
```

Run: uvicorn api:app --reload

Visit http://localhost:8000/docs for auto-generated Swagger UI documentation — one of FastAPI’s killer features.

> **💡 Pydantic Models**
>
> NoteCreate is a Pydantic model. FastAPI uses it to automatically validate incoming JSON, generate documentation, and return helpful error messages if the data is wrong. Type hints (str, list[str]) are not just documentation — they are enforced at runtime.

## Review: Days 19–21

**Daily Checklist**

- [ ] I understand HTTP methods and status codes
- [ ] I know REST conventions (resources, statelessness)
- [ ] I can build a REST API with FastAPI
- [ ] I understand Pydantic models for validation
- [ ] Nexus is a web API accessible from any HTTP client
- [ ] I can use /docs for interactive testing

### Quiz

**Q1: Which HTTP method creates a new resource?**

- A) GET
- B) POST
- C) PUT
- D) DELETE

<details><summary>Answer</summary>

**B** — POST creates. PUT replaces.

</details>

**Q2: What does status code 404 mean?**

- A) Success
- B) Server error
- C) Not found
- D) Unauthorized

<details><summary>Answer</summary>

**C** — The resource does not exist.

</details>

**Q3: What does Pydantic do in FastAPI?**

- A) Database queries
- B) Request validation & serialization
- C) Authentication
- D) Logging

<details><summary>Answer</summary>

**B** — Validates incoming data against type hints.

</details>

**Q4: A client sends a POST request to `/notes` with a JSON body missing the required `title` field. Which status code should FastAPI return?**

- A) 404
- B) 500
- C) 422
- D) 401

<details><summary>Answer</summary>

**C** — FastAPI returns 422 Unprocessable Entity when Pydantic validation fails. The request was well-formed HTTP, but the data did not satisfy the schema constraints.

</details>

## Exercises

**Easy: Add a PATCH endpoint**

Add a `PATCH /notes/{index}` endpoint to `api.py` that accepts a partial update. Only the fields provided in the request body should be updated; omitted fields should remain unchanged. Use a Pydantic model with all fields marked `Optional`.

<details><summary>Solution</summary>

```python
from typing import Optional
from pydantic import BaseModel

class NoteUpdate(BaseModel):
    title: Optional[str] = None
    content: Optional[str] = None
    tags: Optional[list[str]] = None

@app.patch("/notes/{index}")
def update_note(index: int, update: NoteUpdate):
    if index < 0 or index >= len(store.notes):
        raise HTTPException(status_code=404, detail="Not found")
    note = store.notes[index]
    if update.title is not None:
        note.title = update.title
    if update.content is not None:
        note.content = update.content
    if update.tags is not None:
        note.tags = update.tags
    store.save()
    return note.to_dict()
```

The key insight is that `Optional[str] = None` means the field is not required in the incoming JSON. You then check `if update.title is not None` rather than `if update.title` to distinguish between "not provided" and "set to empty string".

</details>

---

**Medium: Add query-parameter filtering by tag**

Extend the `GET /notes` endpoint to support filtering by a `tag` query parameter. If `?tag=work` is supplied, return only notes that contain `"work"` in their tags list. If neither `search` nor `tag` is provided, return all notes.

<details><summary>Solution</summary>

```python
from typing import Optional

@app.get("/notes")
def list_notes(search: Optional[str] = None, tag: Optional[str] = None):
    notes = store.notes

    if search:
        notes = [n for n in notes if search.lower() in n.title.lower()
                 or search.lower() in n.content.lower()]

    if tag:
        notes = [n for n in notes if tag.lower() in [t.lower() for t in n.tags]]

    return [n.to_dict() for n in notes]
```

FastAPI automatically reads `tag` from the query string when you declare it as a function parameter with a default of `None`. Filtering is applied sequentially, so `?search=meeting&tag=work` returns notes that match both conditions.

</details>

---

**Challenge: Write a pytest test suite for the API**

Using `fastapi.testclient.TestClient`, write a test file `tests/test_api.py` that covers: (1) creating a note returns 201 and the correct body, (2) fetching a non-existent index returns 404, (3) creating a note with a missing `title` field returns 422.

<details><summary>Solution</summary>

```python
# tests/test_api.py
import pytest
from fastapi.testclient import TestClient
from api import app

client = TestClient(app)

def test_create_note_returns_201():
    payload = {"title": "Test Note", "content": "Hello", "tags": ["pytest"]}
    response = client.post("/notes", json=payload)
    assert response.status_code == 201
    data = response.json()
    assert data["title"] == "Test Note"
    assert data["content"] == "Hello"
    assert "pytest" in data["tags"]

def test_get_nonexistent_note_returns_404():
    response = client.get("/notes/99999")
    assert response.status_code == 404
    assert response.json()["detail"] == "Not found"

def test_create_note_without_title_returns_422():
    payload = {"content": "Missing title"}
    response = client.post("/notes", json=payload)
    assert response.status_code == 422
    errors = response.json()["detail"]
    fields = [e["loc"][-1] for e in errors]
    assert "title" in fields
```

`TestClient` runs your FastAPI app in-process without starting a real server. Each test is independent. The 422 test inspects the Pydantic error detail to confirm the missing field is specifically `title`, not some other validation failure.

</details>

## Further Reading

- [FastAPI Official Documentation — First Steps](https://fastapi.tiangolo.com/tutorial/first-steps/) — The canonical tutorial covering path operations, Pydantic models, and automatic docs generation directly from the FastAPI authors.
- [Real Python — Python REST APIs With FastAPI](https://realpython.com/fastapi-python-rest-apis/) — A practical end-to-end walkthrough building a REST API with FastAPI, covering request validation, response models, and testing with TestClient.


---

# Days 22–26: SQL and Database Integration

Learn SQL, migrate from JSON to a real database, and understand ORMs. (15 hours)

## Reading: Why Databases?

JSON files have critical limits: you must load the entire file to find one record, multiple processes cannot write safely, and there is no efficient querying or indexing. Databases solve all of this.

A relational database stores data in tables (like spreadsheets) with rows and columns. SQL (Structured Query Language) is how you interact with it. We start with SQLite (built into Python) and later switch to PostgreSQL (production-grade).

## Reading: SQL Fundamentals

### Creating Tables

```
CREATE TABLE notes (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    title TEXT NOT NULL,
    content TEXT DEFAULT '',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

Each column has a name, type, and constraints. PRIMARY KEY uniquely identifies each row. NOT NULL means the value is required. DEFAULT provides a fallback.

### CRUD Operations

```sql
-- Create
INSERT INTO notes (title, content) VALUES ('Meeting', 'Discussed roadmap');

-- Read
SELECT * FROM notes;                              -- all rows
SELECT title FROM notes WHERE id = 1;             -- specific row
SELECT * FROM notes WHERE title LIKE '%meet%';    -- search
SELECT * FROM notes ORDER BY created_at DESC;     -- sorted
SELECT * FROM notes LIMIT 10 OFFSET 20;           -- pagination

-- Update
UPDATE notes SET title = 'Updated' WHERE id = 1;

-- Delete
DELETE FROM notes WHERE id = 1;
```

### Relationships and JOINs

```sql
CREATE TABLE tags (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    note_id INTEGER REFERENCES notes(id),
    name TEXT NOT NULL
);

-- Get notes with their tags
SELECT notes.title, tags.name
FROM notes
JOIN tags ON tags.note_id = notes.id;
```

> **💡 Foreign Keys**
>
> tags.note_id REFERENCES notes(id) creates a foreign key — a link between tables. This enforces that every tag belongs to a valid note. This is called referential integrity.

## Reading: SQLAlchemy ORM

Writing raw SQL strings in Python is error-prone and vulnerable to SQL injection attacks. An ORM (Object-Relational Mapper) lets you use Python classes instead of SQL:

```python
# database.py
from sqlalchemy import create_engine, Column, Integer, String, DateTime
from sqlalchemy.orm import declarative_base, sessionmaker
from datetime import datetime

Base = declarative_base()

class NoteModel(Base):
    __tablename__ = "notes"
    id = Column(Integer, primary_key=True)
    title = Column(String, nullable=False)
    content = Column(String, default="")
    created_at = Column(DateTime, default=datetime.utcnow)

engine = create_engine("sqlite:///nexus.db")
Base.metadata.create_all(engine)
Session = sessionmaker(bind=engine)
```

Now you can query with Python:

```
session = Session()
# Create
note = NoteModel(title="Meeting", content="Notes here")
session.add(note); session.commit()

# Read
all_notes = session.query(NoteModel).all()
note = session.query(NoteModel).filter_by(id=1).first()

# Search
results = session.query(NoteModel).filter(NoteModel.title.ilike("%meet%")).all()

# Delete
session.delete(note); session.commit()
```

## Build: Migrate Nexus to SQLite

Replace JSON storage with SQLAlchemy. Write a migration script that reads nexus_data.json and inserts records into the database. Update all API endpoints to use database sessions. This is your first data migration — a common real-world task.

```bash
git commit -m "Add SQLAlchemy models for notes"
git commit -m "Migrate API endpoints to use database"
git commit -m "Add JSON-to-SQLite migration script"
```

## Review: Days 22–26

**Daily Checklist**

- [ ] I can write SQL: CREATE, INSERT, SELECT, UPDATE, DELETE
- [ ] I understand primary keys, foreign keys, JOINs
- [ ] I can use SQLAlchemy ORM to interact with databases
- [ ] I understand SQL injection and why ORMs prevent it
- [ ] Nexus uses SQLite instead of JSON files
- [ ] I performed my first data migration

### Quiz

**Q1: What does SELECT * FROM notes WHERE id = 1 return?**

- A) All notes
- B) First note only
- C) Note with id=1
- D) Count of notes

<details><summary>Answer</summary>

**C** — WHERE filters to rows matching the condition.

</details>

**Q2: Why use an ORM instead of raw SQL?**

- A) Faster queries
- B) Prevents SQL injection, Python-native interface
- C) Required by databases
- D) Smaller code

<details><summary>Answer</summary>

**B** — Safety and developer experience.

</details>

**Q3: Which SQL clause limits the number of rows returned and is essential for pagination?**

- A) WHERE
- B) LIMIT
- C) GROUP BY
- D) HAVING

<details><summary>Answer</summary>

**B** — `LIMIT n OFFSET m` returns at most `n` rows starting from position `m`. `WHERE` filters rows by condition but does not cap the result count.

</details>

**Q4: You run `DELETE FROM notes` without a WHERE clause. What happens?**

- A) Nothing — DELETE requires a WHERE clause
- B) Only the first row is deleted
- C) Every row in the notes table is deleted
- D) SQLAlchemy raises a Python exception automatically

<details><summary>Answer</summary>

**C** — Every row is deleted. There is no implicit safety guard in standard SQL. This is why application code should always build DELETE statements through an ORM or parameterized queries, and production databases should have role-based permission restrictions.

</details>

## Exercises

**Easy: Write the raw SQL for a tag search**

Without using SQLAlchemy, write the SQL query that returns the `title` and `created_at` of all notes that have at least one tag whose `name` is exactly `'work'`. Assume the schema from the Reading section (tables `notes` and `tags`).

<details><summary>Solution</summary>

```sql
SELECT notes.title, notes.created_at
FROM notes
JOIN tags ON tags.note_id = notes.id
WHERE tags.name = 'work';
```

The `JOIN` links each tag row to its parent note row via the foreign key `tags.note_id = notes.id`. The `WHERE` then filters to only the `'work'` tag. If a note has multiple tags including `'work'`, it appears once per matching tag row — add `DISTINCT` before `notes.title` if you want each note listed only once:

```sql
SELECT DISTINCT notes.title, notes.created_at
FROM notes
JOIN tags ON tags.note_id = notes.id
WHERE tags.name = 'work';
```

</details>

---

**Medium: Add a Tag model and relationship in SQLAlchemy**

Extend `database.py` to add a `TagModel` with a foreign key to `NoteModel`. Add a `tags` relationship on `NoteModel` so that `note.tags` returns the list of associated `TagModel` instances. Then write a function `get_notes_by_tag(session, tag_name: str)` that returns all `NoteModel` objects that have a tag matching the given name.

<details><summary>Solution</summary>

```python
# database.py
from sqlalchemy import create_engine, Column, Integer, String, DateTime, ForeignKey
from sqlalchemy.orm import declarative_base, sessionmaker, relationship
from datetime import datetime

Base = declarative_base()

class TagModel(Base):
    __tablename__ = "tags"
    id = Column(Integer, primary_key=True)
    name = Column(String, nullable=False)
    note_id = Column(Integer, ForeignKey("notes.id"), nullable=False)

class NoteModel(Base):
    __tablename__ = "notes"
    id = Column(Integer, primary_key=True)
    title = Column(String, nullable=False)
    content = Column(String, default="")
    created_at = Column(DateTime, default=datetime.utcnow)
    tags = relationship("TagModel", backref="note", cascade="all, delete-orphan")

engine = create_engine("sqlite:///nexus.db")
Base.metadata.create_all(engine)
Session = sessionmaker(bind=engine)

def get_notes_by_tag(session, tag_name: str):
    return (
        session.query(NoteModel)
        .join(TagModel, TagModel.note_id == NoteModel.id)
        .filter(TagModel.name == tag_name)
        .all()
    )
```

`relationship("TagModel", cascade="all, delete-orphan")` means deleting a `NoteModel` automatically deletes its tags — no orphaned rows. The `backref="note"` gives `TagModel` a `.note` attribute for free, so you can navigate in both directions.

</details>

---

**Challenge: Write a migration script from JSON to SQLite**

Write a standalone Python script `scripts/migrate_json_to_db.py` that reads `nexus_data.json` (format: `{"notes": [{"title": "...", "content": "...", "tags": [...], "created_at": "..."}]}`), inserts each note and its tags into the SQLAlchemy database, and prints a summary of how many notes and tags were migrated. The script must be idempotent — running it twice should not create duplicate records.

<details><summary>Solution</summary>

```python
# scripts/migrate_json_to_db.py
import json
import os
import sys
from datetime import datetime

sys.path.insert(0, os.path.dirname(os.path.dirname(os.path.abspath(__file__))))
from database import NoteModel, TagModel, Session, engine, Base

Base.metadata.create_all(engine)

DATA_FILE = os.path.join(os.path.dirname(__file__), "..", "nexus_data.json")

def migrate():
    with open(DATA_FILE) as f:
        data = json.load(f)

    session = Session()
    notes_migrated = 0
    tags_migrated = 0

    for raw in data.get("notes", []):
        # Idempotency check: skip if a note with this title and content exists
        existing = (
            session.query(NoteModel)
            .filter_by(title=raw["title"], content=raw.get("content", ""))
            .first()
        )
        if existing:
            continue

        note = NoteModel(
            title=raw["title"],
            content=raw.get("content", ""),
            created_at=datetime.fromisoformat(raw["created_at"])
            if raw.get("created_at")
            else datetime.utcnow(),
        )
        session.add(note)
        session.flush()  # Assigns note.id without committing

        for tag_name in raw.get("tags", []):
            session.add(TagModel(name=tag_name, note_id=note.id))
            tags_migrated += 1

        notes_migrated += 1

    session.commit()
    session.close()
    print(f"Migration complete: {notes_migrated} notes, {tags_migrated} tags inserted.")

if __name__ == "__main__":
    migrate()
```

`session.flush()` writes the note to the database within the current transaction so SQLAlchemy can assign an `id`, which the tag rows need for their foreign key. No data is permanently committed until `session.commit()` at the end, so a crash mid-loop rolls back everything cleanly.

</details>

## Further Reading

- [SQLAlchemy ORM Quick Start](https://docs.sqlalchemy.org/en/20/orm/quickstart.html) — The official 2.0-style ORM tutorial covering mapped classes, sessions, relationships, and queries with the modern `select()` style.
- [Real Python — Introduction to Python SQL Libraries](https://realpython.com/python-sql-libraries/) — Covers SQLite3, SQLAlchemy Core, and ORM side-by-side so you understand what the ORM is doing under the hood.


---

# Days 27–32: Async Programming and Docker

Handle many requests simultaneously and containerize your application. (18 hours)

## Reading: Sync vs Async

Synchronous code: one thing at a time. When waiting for a database query, the entire program blocks. Asynchronous code: start a task, do other work while waiting, come back when it completes.

Analogy: sync is one cashier serving one customer at a time. Async is one cashier who starts an order, lets the kitchen cook while taking the next customer’s order, then delivers food as it’s ready.

```python
# Synchronous (blocks)
def get_notes():
    result = database.query(...)  # waits 200ms, does NOTHING else
    return result

# Asynchronous (non-blocking)
async def get_notes():
    result = await database.query(...)  # waits 200ms, handles OTHER requests
    return result
```

The async and await keywords are Python’s async syntax. async def declares a coroutine. await pauses the coroutine and yields control back to the event loop, which can run other coroutines. This is called cooperative multitasking.

### When Does Async Matter?

Async helps with I/O-bound tasks: database queries, HTTP requests, file reads. It does NOT help with CPU-bound tasks (math, image processing) — for those you need multiprocessing. Web APIs are almost entirely I/O-bound, making async a huge win.

## Reading: Environment Variables

Never hardcode configuration (database URLs, ports, secrets) in your code. Use environment variables:

```python
# .env file (NEVER commit this)
DATABASE_URL=sqlite:///nexus.db
SECRET_KEY=your-secret-key-here
DEBUG=true

# Python code
import os
from dotenv import load_dotenv
load_dotenv()  # pip install python-dotenv

db_url = os.getenv("DATABASE_URL", "sqlite:///nexus.db")
debug = os.getenv("DEBUG", "false").lower() == "true"
```

## Reading: Docker

Docker packages your application and ALL dependencies into a container — a lightweight, isolated environment that runs identically everywhere. This eliminates "it works on my machine" problems.

### Key Concepts

An Image is a read-only template (like a class). A Container is a running instance of an image (like an object). A Dockerfile is the recipe to build an image. docker-compose.yml orchestrates multiple containers.

```dockerfile
# Dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["uvicorn", "api:app", "--host", "0.0.0.0", "--port", "8000"]
```

```yaml
# docker-compose.yml
version: '3.8'
services:
  api:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/nexus
    depends_on:
      - db
  db:
    image: postgres:16
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: nexus
    volumes:
      - pgdata:/var/lib/postgresql/data
volumes:
  pgdata:
```

```bash
docker-compose up --build      # Build and start all services
docker-compose down            # Stop all services
docker-compose logs -f api     # Follow API logs
```

## Build: Dockerized Async Nexus

Convert database operations to async using SQLAlchemy’s async engine. Add environment variables via .env. Create Dockerfile and docker-compose.yml. Switch from SQLite to PostgreSQL. Your API now runs in a container talking to a containerized database.

## Review: Days 27–32

**Daily Checklist**

- [ ] I understand sync vs async and when async matters
- [ ] I can use async/await in Python and FastAPI
- [ ] I never hardcode config — I use environment variables
- [ ] I understand Docker images, containers, Dockerfiles
- [ ] I can use docker-compose for multi-service apps
- [ ] Nexus runs in Docker with PostgreSQL

### Quiz

**Q1: When does async help performance?**

- A) CPU-bound tasks
- B) I/O-bound tasks (DB, network)
- C) Always
- D) Never in Python

<details><summary>Answer</summary>

**B** — Async shines when waiting on I/O.

</details>

**Q2: What is a Docker container?**

- A) A virtual machine
- B) A running instance of an image
- C) A type of database
- D) A Python package

<details><summary>Answer</summary>

**B** — Image = blueprint, Container = running instance.

</details>

**Q3: In a `docker-compose.yml`, what does `depends_on: [db]` guarantee?**

- A) The `api` container waits until the database is fully ready to accept connections
- B) The `api` container starts after the `db` container process has been launched
- C) Docker automatically restarts `api` if `db` crashes
- D) The two containers share the same network namespace

<details><summary>Answer</summary>

**B** — `depends_on` only waits for the container process to start, not for the service inside it to be ready. PostgreSQL takes a few seconds to initialize after its container starts. In production you need a health check or a retry loop to handle this gap.

</details>

**Q4: What is the Python event loop's role in async/await code?**

- A) It spawns a new OS thread for each `await` expression
- B) It schedules and runs coroutines on a single thread, switching between them at `await` points
- C) It compiles `async def` functions into C extensions for speed
- D) It distributes coroutines across multiple CPU cores

<details><summary>Answer</summary>

**B** — The event loop is a single-threaded scheduler. When a coroutine hits `await`, it pauses and yields control back to the loop, which can then run another coroutine that is ready. No threads are created. This is why async does not help CPU-bound work — the CPU is always busy on one thread.

</details>

## Exercises

**Easy: Convert a synchronous FastAPI route to async**

The following route uses a synchronous SQLAlchemy session and blocks the event loop. Rewrite it using SQLAlchemy's `AsyncSession` and `async_sessionmaker` so it is fully non-blocking. Assume `async_engine` has already been created with `create_async_engine`.

```python
# Before (synchronous)
@app.get("/notes/{note_id}")
def get_note(note_id: int):
    session = Session()
    note = session.query(NoteModel).filter_by(id=note_id).first()
    session.close()
    if note is None:
        raise HTTPException(status_code=404, detail="Not found")
    return {"id": note.id, "title": note.title}
```

<details><summary>Solution</summary>

```python
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession, async_sessionmaker
from sqlalchemy import select

async_engine = create_async_engine("postgresql+asyncpg://user:pass@db:5432/nexus")
AsyncSessionLocal = async_sessionmaker(async_engine, expire_on_commit=False)

@app.get("/notes/{note_id}")
async def get_note(note_id: int):
    async with AsyncSessionLocal() as session:
        result = await session.execute(
            select(NoteModel).where(NoteModel.id == note_id)
        )
        note = result.scalar_one_or_none()
    if note is None:
        raise HTTPException(status_code=404, detail="Not found")
    return {"id": note.id, "title": note.title}
```

Three key changes: (1) `async def` instead of `def`, (2) `await session.execute(select(...))` instead of `session.query(...).first()` — SQLAlchemy 2.0 async uses `select()` not `.query()`, (3) `async with` ensures the session is closed even if an exception is raised.

</details>

---

**Medium: Add a `.env` file and load config with pydantic-settings**

Create a `.env` file with `DATABASE_URL`, `SECRET_KEY`, and `DEBUG` variables. Then create a `config.py` module that uses `pydantic-settings` `BaseSettings` to load these values with proper types. Import and use the settings object in `api.py`. Add `.env` to `.gitignore`.

<details><summary>Solution</summary>

```bash
# .env  (never commit this file)
DATABASE_URL=postgresql+asyncpg://user:pass@db:5432/nexus
SECRET_KEY=change-me-in-production-use-openssl-rand-hex-32
DEBUG=false
```

```python
# config.py
from pydantic_settings import BaseSettings  # pip install pydantic-settings

class Settings(BaseSettings):
    database_url: str
    secret_key: str
    debug: bool = False

    class Config:
        env_file = ".env"
        env_file_encoding = "utf-8"

settings = Settings()
```

```python
# api.py
from config import settings

async_engine = create_async_engine(settings.database_url)

if settings.debug:
    import logging
    logging.basicConfig(level=logging.DEBUG)
```

```text
# .gitignore  (add this line)
.env
```

`BaseSettings` automatically reads from environment variables first, then from the `.env` file. The type annotations (`bool`, `str`) coerce the string values from the file — `"false"` becomes `False`. This means the same config works locally (`.env` file) and in Docker (environment variables injected by docker-compose).

</details>

---

**Challenge: Write a production-ready Dockerfile with a non-root user and multi-stage build**

Write a `Dockerfile` for the Nexus API that: (1) uses a `builder` stage to install dependencies into a virtual environment, (2) copies only the venv into a minimal final `python:3.12-slim` image, (3) runs the application as a non-root user named `nexus`. Explain why each of these three choices matters for production.

<details><summary>Solution</summary>

```dockerfile
# Dockerfile

# --- Stage 1: builder ---
FROM python:3.12-slim AS builder

WORKDIR /build

# Install dependencies into an isolated venv
RUN python -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# --- Stage 2: final image ---
FROM python:3.12-slim

# Create a non-root user
RUN useradd --create-home --shell /bin/bash nexus

WORKDIR /app

# Copy only the venv from the builder — no build tools in the final image
COPY --from=builder /opt/venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

# Copy application code
COPY --chown=nexus:nexus . .

# Switch to non-root user
USER nexus

EXPOSE 8000
CMD ["uvicorn", "api:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "2"]
```

**Why each choice matters:**

1. **Multi-stage build** — The `builder` stage has `pip`, compilers, and build caches. None of that belongs in production. The final image contains only the venv and your code, reducing image size by 200–400 MB and shrinking the attack surface.

2. **Virtual environment in the builder** — Isolates dependencies to `/opt/venv`, making it trivial to `COPY --from=builder` just that directory. The system Python in the final image is untouched.

3. **Non-root user** — If an attacker exploits a vulnerability in your app, they get the `nexus` user's permissions, not root. A root compromise inside a container can escape the container via kernel exploits. Running as non-root is a Docker security baseline requirement in most organizations.

</details>

## Further Reading

- [Python asyncio — Official Documentation](https://docs.python.org/3/library/asyncio.html) — The complete reference for Python's async runtime: event loop, coroutines, tasks, and synchronization primitives, with worked examples.
- [Docker — Multi-stage Builds](https://docs.docker.com/build/building/multi-stage/) — Official guide to multi-stage Dockerfiles, including how to minimize final image size and why separating build-time from run-time dependencies matters.


---

# Days 33–38: Authentication and Production API Design

Security, middleware, and production-grade API patterns. (18 hours)

## Reading: Authentication vs Authorization

Authentication (AuthN) answers "who are you?" — verifying identity. Authorization (AuthZ) answers "what can you do?" — verifying permissions. You log in (AuthN), then the system checks if you can delete that note (AuthZ).

### JWT: JSON Web Tokens

JWT is a stateless authentication mechanism. The server creates a signed token containing user info. The client stores it and sends it with every request. The server verifies the signature without needing a database lookup.

A JWT has three parts separated by dots: Header (algorithm), Payload (user data, expiration), Signature (cryptographic proof it has not been tampered with).

```python
# Generate token
from jose import jwt
from datetime import datetime, timedelta

SECRET_KEY = os.getenv("SECRET_KEY")
def create_token(user_id: int) -> str:
    payload = {
        "sub": str(user_id),
        "exp": datetime.utcnow() + timedelta(hours=24)
    }
    return jwt.encode(payload, SECRET_KEY, algorithm="HS256")

# Verify token
def get_current_user(token: str):
    payload = jwt.decode(token, SECRET_KEY, algorithms=["HS256"])
    return int(payload["sub"])
```

## Reading: Middleware

Middleware is code that runs before and/or after every request. Use it for logging, timing, authentication headers, CORS:

```python
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:3000"],
    allow_methods=["*"],
    allow_headers=["*"],
)

@app.middleware("http")
async def log_requests(request, call_next):
    start = time.time()
    response = await call_next(request)
    duration = time.time() - start
    print(f"{request.method} {request.url.path} - {duration:.3f}s")
    return response
```

### CORS Explained

Cross-Origin Resource Sharing: browsers block requests from one domain to another by default (security). If your frontend is at localhost:3000 and API at localhost:8000, you must explicitly allow it with CORS middleware.

## Reading: Pagination

Never return all records at once. Use offset-based or cursor-based pagination:

```python
@app.get("/notes")
async def list_notes(page: int = 1, per_page: int = 20):
    offset = (page - 1) * per_page
    notes = await db.query(Note).offset(offset).limit(per_page).all()
    total = await db.query(Note).count()
    return {
        "data": notes,
        "page": page,
        "per_page": per_page,
        "total": total,
        "pages": (total + per_page - 1) // per_page
    }
```

## Build: Secure Multi-User Nexus

Add user registration (hashed passwords with bcrypt) and login endpoints. Implement JWT auth. Each user accesses only their own notes (foreign key user_id). Add request logging middleware and timing. Implement pagination on /notes. Add rate limiting.

## Review: Days 33–38

**Daily Checklist**

- [ ] I understand authentication (who) vs authorization (what)
- [ ] I can implement JWT auth in FastAPI
- [ ] I understand middleware and wrote custom middleware
- [ ] I know what CORS is and configured it
- [ ] I implemented pagination and rate limiting
- [ ] Nexus is a secure multi-user API

### Quiz

**Q1: What does JWT stand for?**

- A) Java Web Token
- B) JSON Web Token
- C) JavaScript Worker Thread
- D) Joint Web Transfer

<details><summary>Answer</summary>

**B** — JSON Web Token.

</details>

**Q2: What is CORS?**

- A) A database type
- B) Browser security that blocks cross-origin requests
- C) A Python library
- D) A Docker feature

<details><summary>Answer</summary>

**B** — Browsers enforce it; CORS middleware relaxes it.

</details>

**Q3: A user's JWT has expired. Which HTTP status code should the server return?**

- A) 400 Bad Request
- B) 403 Forbidden
- C) 401 Unauthorized
- D) 404 Not Found

<details><summary>Answer</summary>

**C** — 401 Unauthorized means the request lacks valid authentication credentials. An expired token is an authentication failure. 403 Forbidden would be correct if the token were valid but the user lacked permission for that specific resource.

</details>

**Q4: Why should passwords be hashed with bcrypt rather than stored as plain text or hashed with SHA-256?**

- A) bcrypt produces shorter hashes, saving database space
- B) bcrypt is reversible, allowing password recovery
- C) bcrypt is intentionally slow and includes a salt, making brute-force attacks computationally expensive
- D) bcrypt is required by the HTTP specification

<details><summary>Answer</summary>

**C** — bcrypt's work factor makes each hash computation take ~100 ms, so testing one million passwords takes ~28 hours. SHA-256 is designed to be fast — a GPU can test billions of SHA-256 hashes per second. bcrypt also generates a unique random salt per password, so two users with the same password have different hashes, defeating rainbow table attacks.

</details>

## Exercises

**Easy: Implement a password hashing utility module**

Create `auth/passwords.py` with two functions: `hash_password(plain: str) -> str` that hashes a password with bcrypt, and `verify_password(plain: str, hashed: str) -> bool` that checks a plain-text password against a stored hash. Write a short `if __name__ == "__main__"` block that demonstrates both functions.

<details><summary>Solution</summary>

```python
# auth/passwords.py
# pip install passlib[bcrypt]
from passlib.context import CryptContext

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

def hash_password(plain: str) -> str:
    """Return a bcrypt hash of the plain-text password."""
    return pwd_context.hash(plain)

def verify_password(plain: str, hashed: str) -> bool:
    """Return True if plain matches the bcrypt hash."""
    return pwd_context.verify(plain, hashed)

if __name__ == "__main__":
    password = "s3cur3P@ssw0rd"
    hashed = hash_password(password)
    print(f"Hash:   {hashed}")
    print(f"Match:  {verify_password(password, hashed)}")         # True
    print(f"Wrong:  {verify_password('wrongpassword', hashed)}")  # False
```

`passlib.context.CryptContext` abstracts the hashing algorithm. Using `passlib` rather than `bcrypt` directly means you can migrate to a stronger algorithm later by adding it to `schemes` — existing hashes still verify, and new hashes use the new algorithm (`deprecated="auto"` handles the transition automatically).

</details>

---

**Medium: Add JWT authentication to a FastAPI route using Depends**

Add a `POST /auth/login` endpoint that accepts `username` and `password` form fields, verifies the password, and returns a JWT access token. Then write a `get_current_user` dependency that extracts and validates the JWT from the `Authorization: Bearer <token>` header. Protect `GET /notes` with this dependency.

<details><summary>Solution</summary>

```python
# api.py additions
import os
import time
from fastapi import Depends, Security
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials
from fastapi import Form
from jose import jwt, JWTError
from auth.passwords import verify_password, hash_password

SECRET_KEY = os.getenv("SECRET_KEY", "dev-secret-change-me")
ALGORITHM = "HS256"
TOKEN_EXPIRE_SECONDS = 86400  # 24 hours

# Fake user store — replace with DB lookup in production
FAKE_USERS = {
    "alice": hash_password("correct-horse-battery"),
}

bearer_scheme = HTTPBearer()

def create_access_token(username: str) -> str:
    payload = {
        "sub": username,
        "exp": int(time.time()) + TOKEN_EXPIRE_SECONDS,
    }
    return jwt.encode(payload, SECRET_KEY, algorithm=ALGORITHM)

def get_current_user(
    credentials: HTTPAuthorizationCredentials = Security(bearer_scheme),
) -> str:
    token = credentials.credentials
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        username: str = payload.get("sub")
        if username is None:
            raise HTTPException(status_code=401, detail="Invalid token")
        return username
    except JWTError:
        raise HTTPException(status_code=401, detail="Invalid or expired token")

@app.post("/auth/login")
def login(username: str = Form(...), password: str = Form(...)):
    stored_hash = FAKE_USERS.get(username)
    if stored_hash is None or not verify_password(password, stored_hash):
        raise HTTPException(status_code=401, detail="Invalid credentials")
    token = create_access_token(username)
    return {"access_token": token, "token_type": "bearer"}

@app.get("/notes")
async def list_notes(current_user: str = Depends(get_current_user)):
    # current_user is the username extracted from the validated JWT
    return [n.to_dict() for n in store.notes]
```

`Depends(get_current_user)` tells FastAPI to call `get_current_user` before the route handler and inject its return value. If `get_current_user` raises an `HTTPException`, the route never runs. This pattern means you can protect any route by adding one `Depends` argument.

</details>

---

**Challenge: Implement a sliding-window rate limiter as FastAPI middleware**

Write a middleware class `RateLimitMiddleware` that limits each client IP to 60 requests per 60-second window. Use an in-memory dictionary keyed by IP address, storing a list of request timestamps. Requests exceeding the limit should receive a `429 Too Many Requests` response with a `Retry-After` header. Register it on the FastAPI app.

<details><summary>Solution</summary>

```python
# middleware/rate_limit.py
import time
from collections import defaultdict
from starlette.middleware.base import BaseHTTPMiddleware
from starlette.responses import JSONResponse

class RateLimitMiddleware(BaseHTTPMiddleware):
    def __init__(self, app, max_requests: int = 60, window_seconds: int = 60):
        super().__init__(app)
        self.max_requests = max_requests
        self.window_seconds = window_seconds
        self._requests: dict[str, list[float]] = defaultdict(list)

    async def dispatch(self, request, call_next):
        client_ip = request.client.host
        now = time.time()
        window_start = now - self.window_seconds

        # Drop timestamps outside the current window (sliding window)
        timestamps = self._requests[client_ip]
        self._requests[client_ip] = [t for t in timestamps if t > window_start]

        if len(self._requests[client_ip]) >= self.max_requests:
            oldest = self._requests[client_ip][0]
            retry_after = int(oldest + self.window_seconds - now) + 1
            return JSONResponse(
                status_code=429,
                content={"detail": "Rate limit exceeded. Try again later."},
                headers={"Retry-After": str(retry_after)},
            )

        self._requests[client_ip].append(now)
        return await call_next(request)
```

```python
# api.py
from middleware.rate_limit import RateLimitMiddleware

app.add_middleware(RateLimitMiddleware, max_requests=60, window_seconds=60)
```

The sliding window is more accurate than a fixed window (which allows 2x bursts at window boundaries). For a production deployment, replace the in-memory dictionary with Redis using `ZADD` / `ZREMRANGEBYSCORE` / `ZCARD` so the rate limit is shared across multiple API worker processes.

</details>

## Further Reading

- [Real Python — Token-Based Authentication with FastAPI and JWT](https://realpython.com/fastapi-authentication/) — A complete walkthrough of implementing user registration, bcrypt hashing, JWT issuance, and protected routes in FastAPI, matching exactly the pattern built in this session.
- [JWT.io Introduction](https://jwt.io/introduction) — The authoritative reference explaining JWT structure (header, payload, signature), signing algorithms (HS256 vs RS256), and common security mistakes to avoid.


---

# Phase 2 Complete: What You Have Built

**Daily Checklist**

- [ ] A REST API with FastAPI serving JSON over HTTP
- [ ] PostgreSQL database with SQLAlchemy ORM
- [ ] Async request handling for performance
- [ ] JWT authentication and user management
- [ ] CORS, rate limiting, pagination, logging middleware
- [ ] Docker containerization with docker-compose
- [ ] Environment-based configuration (.env)
- [ ] Data migration from JSON to relational database

Your GitHub repo should have 60–80+ commits. The git log tells the story of a CLI tool that evolved into a production web service.

> **📚 Concepts to Deepen on Your Own**
>
> • HTTP/2 and HTTP/3 protocols
> • WebSockets for real-time communication
> • GraphQL as an alternative to REST
> • Database indexing and query optimization (EXPLAIN)
> • Database migrations with Alembic
> • Connection pooling and database performance
> • OAuth 2.0 and OpenID Connect
> • API versioning strategies (URL path, headers, query params)
> • OpenAPI/Swagger specification in depth
> • Docker multi-stage builds for smaller images
> • Docker networking and volumes
> • CI/CD pipelines (GitHub Actions)
> • Load testing with locust or k6
> • Caching strategies (Redis, HTTP cache headers)
> • HTTPS, TLS, and certificate management
