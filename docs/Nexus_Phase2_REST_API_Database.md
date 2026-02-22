# Introduction to Phase 2

In Phase 1, Nexus was a CLI tool that only you could use, on your machine. In Phase 2, it becomes a web service that any program or device on the network can talk to. You will learn the protocols that power the internet (HTTP), the language of databases (SQL), the art of async programming, and how to package everything in Docker containers.

By the end of Phase 2, Nexus will be a multi-user REST API backed by PostgreSQL, running in Docker, with JWT authentication and proper security.

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

> **🧠 Quiz**
>
> **Q1: Which HTTP method creates a new resource?**
> A) GET
> B) POST
> C) PUT
> D) DELETE
> ✅ Answer: B — POST creates. PUT replaces.
>
> **Q2: What does status code 404 mean?**
> A) Success
> B) Server error
> C) Not found
> D) Unauthorized
> ✅ Answer: C — The resource does not exist.
>
> **Q3: What does Pydantic do in FastAPI?**
> A) Database queries
> B) Request validation & serialization
> C) Authentication
> D) Logging
> ✅ Answer: B — Validates incoming data against type hints.
>

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

> **🧠 Quiz**
>
> **Q1: What does SELECT * FROM notes WHERE id = 1 return?**
> A) All notes
> B) First note only
> C) Note with id=1
> D) Count of notes
> ✅ Answer: C — WHERE filters to rows matching the condition.
>
> **Q2: Why use an ORM instead of raw SQL?**
> A) Faster queries
> B) Prevents SQL injection, Python-native interface
> C) Required by databases
> D) Smaller code
> ✅ Answer: B — Safety and developer experience.
>

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

```sql
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

> **🧠 Quiz**
>
> **Q1: When does async help performance?**
> A) CPU-bound tasks
> B) I/O-bound tasks (DB, network)
> C) Always
> D) Never in Python
> ✅ Answer: B — Async shines when waiting on I/O.
>
> **Q2: What is a Docker container?**
> A) A virtual machine
> B) A running instance of an image
> C) A type of database
> D) A Python package
> ✅ Answer: B — Image = blueprint, Container = running instance.
>

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

> **🧠 Quiz**
>
> **Q1: What does JWT stand for?**
> A) Java Web Token
> B) JSON Web Token
> C) JavaScript Worker Thread
> D) Joint Web Transfer
> ✅ Answer: B — JSON Web Token.
>
> **Q2: What is CORS?**
> A) A database type
> B) Browser security that blocks cross-origin requests
> C) A Python library
> D) A Docker feature
> ✅ Answer: B — Browsers enforce it; CORS middleware relaxes it.
>

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
