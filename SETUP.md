# Setup Guide

Install only what you need for the phase you are starting. Come back here when you reach the next phase.

---

## System Requirements Overview

| Phase | Min RAM | Disk Space | OS |
|-------|---------|------------|----|
| Phase 1 | 4 GB | 2 GB free | Windows 10+, macOS 11+, Ubuntu 20.04+ |
| Phase 2 | 8 GB | 10 GB free | Same + Docker support |
| Phase 3 | 8 GB (16 GB recommended) | 15 GB free | Same — SSD strongly recommended |
| Phase 4 | 16 GB recommended | 20 GB free | Same |

---

## Phase 1 — Python CLI (Days 1–18)

### 1. Python 3.12+

**Windows**
1. Go to [python.org/downloads](https://python.org/downloads)
2. Download the latest Python 3.12.x installer
3. Run it — **check the box "Add python.exe to PATH"** before clicking Install
4. Open a new terminal and verify:

```bash
python --version
# Expected: Python 3.12.x
```

**macOS**
macOS ships with Python 2 (or a stub). Install Python 3 via the official installer or Homebrew:

```bash
# Option A: Official installer (recommended for beginners)
# Download from python.org/downloads, run the .pkg file

# Option B: Homebrew
brew install python@3.12

python3 --version
# Expected: Python 3.12.x
```

> On macOS, you may need to type `python3` instead of `python`. Both commands are used interchangeably in the guide — use whichever works on your machine.

**Linux (Ubuntu/Debian)**
```bash
sudo apt update
sudo apt install python3.12 python3.12-venv python3-pip
python3.12 --version
```

---

### 2. Git

**Windows**
Download from [git-scm.com](https://git-scm.com/downloads). During install, accept all defaults.

```bash
git --version
# Expected: git version 2.x.x
```

**macOS**
```bash
# Comes with Xcode Command Line Tools
xcode-select --install
git --version
```

**Linux**
```bash
sudo apt install git
git --version
```

**First-time Git setup (all platforms)**
```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

---

### 3. VS Code

Download from [code.visualstudio.com](https://code.visualstudio.com). After installing:

1. Open VS Code
2. Click the Extensions icon (or press `Ctrl+Shift+X` / `Cmd+Shift+X`)
3. Search for **Python** by Microsoft — install it
4. Search for **GitLens** — install it (optional but excellent for visualising Git history)

Verify the Python extension is working: open any `.py` file, and you should see the Python version in the status bar at the bottom.

---

### 4. pytest

Install inside a virtual environment (you will learn to create one on Day 1):

```bash
python -m venv venv
source venv/bin/activate        # macOS/Linux
venv\Scripts\activate           # Windows

pip install pytest
pytest --version
# Expected: pytest 8.x.x
```

**Phase 1 is now ready. Start at Day 1 in the [Phase 1 guide](./docs/Nexus_Phase1_CLI_File_Manager.md).**

---

## Phase 2 — REST API + Database (Days 19–38)

Complete Phase 1 setup first. Then add:

### 1. Docker Desktop

Docker runs PostgreSQL and your API in isolated containers — no manual database installation needed.

Download from [docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop).

> **Windows note:** Docker Desktop requires WSL 2. The installer will prompt you to enable it if needed.

Verify after installing and opening Docker Desktop:

```bash
docker --version
# Expected: Docker version 26.x.x
docker compose version
# Expected: Docker Compose version v2.x.x
```

Run a quick smoke test:

```bash
docker run hello-world
# Expected: "Hello from Docker!" message
```

---

### 2. Python Packages for Phase 2

Inside your project virtual environment:

```bash
pip install fastapi "uvicorn[standard]" sqlalchemy python-dotenv passlib[bcrypt] "python-jose[cryptography]" httpx
```

| Package | Purpose |
|---------|---------|
| `fastapi` | Web framework for building the REST API |
| `uvicorn[standard]` | ASGI server that runs FastAPI |
| `sqlalchemy` | ORM for database access |
| `python-dotenv` | Load environment variables from `.env` files |
| `passlib[bcrypt]` | Password hashing for user auth |
| `python-jose[cryptography]` | JWT token creation and verification |
| `httpx` | Async HTTP client (used in tests) |

Verify FastAPI works:

```bash
python -c "import fastapi; print(fastapi.__version__)"
```

---

### 3. PostgreSQL (via Docker — no local install needed)

You will run PostgreSQL inside a Docker container using `docker-compose.yml` (covered on Day 27–32 in the [Phase 2 guide](./docs/Nexus_Phase2_REST_API_Database.md)). No separate PostgreSQL installation is required.

If you want a GUI for inspecting your database:
- **TablePlus** (macOS/Windows) — [tableplus.com](https://tableplus.com) — free tier is sufficient
- **DBeaver** (all platforms, fully free) — [dbeaver.io](https://dbeaver.io)

**Phase 2 is now ready. Open the [Phase 2 guide](./docs/Nexus_Phase2_REST_API_Database.md) to begin Day 19.**

---

## Phase 3 — AI Brain (Days 39–52)

Complete Phase 2 setup first. Phase 3 is the most resource-intensive phase.

### System Requirements Check

Before proceeding, verify:

```bash
# Check available RAM (you need at least 8 GB free while running)
# macOS:
system_profiler SPHardwareDataType | grep Memory
# Linux:
free -h
# Windows: Task Manager > Performance > Memory
```

An SSD is strongly recommended. The embedding model download (~90 MB) and ChromaDB index writes are slow on spinning disks.

---

### 1. Python Packages for Phase 3

```bash
pip install sentence-transformers chromadb openai numpy
```

| Package | Purpose | Size |
|---------|---------|------|
| `sentence-transformers` | Local embedding model (`all-MiniLM-L6-v2`) | ~90 MB model download on first run |
| `chromadb` | Local vector database for semantic search | ~50 MB |
| `openai` | OpenAI API client (GPT, embeddings) | small |
| `numpy` | Numerical operations for vector math | ~20 MB |

**First run will download the embedding model** (~90 MB). Make sure you have an internet connection the first time you run code that uses `SentenceTransformer("all-MiniLM-L6-v2")`. Subsequent runs use the cached model.

Verify the model loads:

```bash
python -c "
from sentence_transformers import SentenceTransformer
model = SentenceTransformer('all-MiniLM-L6-v2')
print(model.encode('hello world').shape)
# Expected: (384,)
"
```

---

### 2. OpenAI API Key

The RAG pipeline calls an LLM to generate answers. You need an OpenAI API key (or configure a local alternative — see below).

1. Create an account at [platform.openai.com](https://platform.openai.com)
2. Go to **API Keys** and create a new secret key
3. Add a small amount of credit ($5–10 is more than enough for the entire phase)
4. Store the key in your project's `.env` file:

```bash
# .env  (never commit this file)
OPENAI_API_KEY=sk-...your-key-here...
```

Add `.env` to your `.gitignore` immediately:

```bash
echo ".env" >> .gitignore
```

Verify the key works:

```bash
python -c "
from openai import OpenAI
client = OpenAI()
r = client.chat.completions.create(
    model='gpt-4o-mini',
    messages=[{'role': 'user', 'content': 'Say hello'}]
)
print(r.choices[0].message.content)
"
```

---

### 3. Alternative: Run LLMs Locally with Ollama (no API cost)

If you prefer not to use OpenAI, you can run open-source models locally with [Ollama](https://ollama.com):

```bash
# Install Ollama (macOS)
brew install ollama

# Pull a model (Llama 3.2 3B — small, fast, ~2 GB)
ollama pull llama3.2:3b

# Install the Python client
pip install ollama
```

> Ollama requires at least 8 GB RAM. The 3B model runs on CPU. For the 8B model, 16 GB RAM is recommended. If you have an Apple Silicon Mac or an NVIDIA GPU, models run significantly faster.

**Phase 3 is now ready. Open the [Phase 3 guide](./docs/Nexus_Phase3_AI_Brain.md) to begin Day 39.**

---

## Phase 4 — Autonomous Agent (Days 53–65)

Complete Phase 3 setup first.

### 1. Python Packages for Phase 4

```bash
pip install langgraph langchain-core celery redis "flower"
```

| Package | Purpose |
|---------|---------|
| `langgraph` | Framework for building stateful AI agent graphs |
| `langchain-core` | Core abstractions used by LangGraph |
| `celery` | Distributed task queue for background jobs |
| `redis` | Python client for the Redis message broker |
| `flower` | Web UI for monitoring Celery workers |

---

### 2. Redis (via Docker)

Celery uses Redis as its message broker. Run Redis in a Docker container:

```bash
docker run -d --name redis -p 6379:6379 redis:7-alpine
```

Verify Redis is running:

```bash
docker exec -it redis redis-cli ping
# Expected: PONG
```

Or add Redis to your existing `docker-compose.yml`:

```yaml
services:
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
```

---

### 3. Verify the Full Stack

Run this to confirm everything from Phases 2–4 is available:

```bash
python -c "
import fastapi, sqlalchemy, chromadb, langgraph, celery
print('FastAPI:', fastapi.__version__)
print('SQLAlchemy:', sqlalchemy.__version__)
print('LangGraph:', langgraph.__version__)
print('Celery:', celery.__version__)
print('All Phase 2–4 packages OK')
"
```

**Phase 4 is now ready. Open the [Phase 4 guide](./docs/Nexus_Phase4_Autonomous_Agent.md) to begin Day 53.**

---

## Keeping Dependencies Organised

Each phase should have its own `requirements.txt` committed to your Nexus repo:

```bash
pip freeze > requirements.txt
git add requirements.txt
git commit -m "Add Phase N dependencies"
```

When reproducing the environment on a new machine:

```bash
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

---

## Troubleshooting Common Issues

### `python` not found / wrong version

```bash
# Check what's on your PATH
which python && python --version
which python3 && python3 --version

# Use the explicit version if needed
python3.12 --version
```

On Windows, run `py -3.12 --version` if the `python` command doesn't work.

---

### Virtual environment not activated

Symptoms: packages you installed aren't found; `import fastapi` fails even after `pip install fastapi`.

```bash
# You should see (venv) at the start of your prompt when active
source venv/bin/activate    # macOS/Linux
venv\Scripts\activate       # Windows

# Confirm pip is the venv pip, not the system pip
which pip   # Should show a path inside your venv folder
```

---

### Docker Desktop not starting

- **Windows:** Make sure WSL 2 is enabled (`wsl --install` in an admin PowerShell)
- **macOS:** Ensure virtualisation is not disabled; check System Settings > Privacy & Security
- **Linux:** Add your user to the `docker` group: `sudo usermod -aG docker $USER` then log out and back in

---

### ChromaDB / sentence-transformers install fails

These packages have compiled extensions. Make sure you have a C compiler:

```bash
# macOS
xcode-select --install

# Ubuntu/Debian
sudo apt install build-essential python3-dev

# Windows — install "Desktop development with C++" from Visual Studio Build Tools
# https://visualstudio.microsoft.com/visual-cpp-build-tools/
```

Then retry: `pip install sentence-transformers chromadb`

---

### OpenAI API returns 401 Unauthorized

Your key is not being read. Check:

```bash
# Confirm the .env file exists and has the key
cat .env | grep OPENAI

# Confirm python-dotenv is loading it
python -c "from dotenv import load_dotenv; import os; load_dotenv(); print(os.getenv('OPENAI_API_KEY', 'NOT FOUND'))"
```

Also confirm you have billing set up at [platform.openai.com/account/billing](https://platform.openai.com/account/billing) — a key without credit returns 429, not 401.
