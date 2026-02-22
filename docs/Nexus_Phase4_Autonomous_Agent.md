# Introduction to Phase 4

In Phase 3, Nexus answered questions. In Phase 4, it takes actions. The difference is autonomy: instead of just retrieving and responding, Nexus can browse the web, send emails, schedule tasks, and make decisions about which tools to use — all without step-by-step instructions from you.

This is the frontier of modern AI engineering: building systems that can reason, plan, and act. By the end of Phase 4, Nexus is a distributed, autonomous agent system deployed to the cloud.

# Days 53–56: LangGraph and Agent Architecture

Learn what AI agents are and build Nexus’s agent brain with LangGraph. (12 hours)

## Reading: Chatbot vs Agent

A chatbot responds to messages. An agent takes actions. A chatbot can tell you the weather. An agent can check the weather, decide you need an umbrella, find the nearest store, and add "buy umbrella" to your todo list.

The key components of an agent: a reasoning engine (LLM that decides what to do), tools (functions the agent can call), memory (context from previous steps), and a planning loop (the agent reasons, acts, observes results, and decides what to do next).

### The ReAct Pattern

Most agents follow the ReAct (Reasoning + Acting) loop:

1. Thought: The LLM reasons about what to do next.

2. Action: It chooses a tool and provides inputs.

3. Observation: The tool executes and returns results.

4. Repeat until the task is complete or the agent decides it has enough info to answer.

## Reading: LangGraph

LangGraph models agents as state machines (graphs). Nodes are actions: call an LLM, use a tool, check a condition. Edges define flow between them. This is more structured than simple prompt chaining and gives you explicit control over the agent’s decision-making.

```bash
pip install langgraph langchain-openai
```

### Key Concepts

State: A dictionary that flows through the graph, accumulating information at each step.

Nodes: Functions that receive state, do work, and return updated state.

Edges: Define which node runs next (can be conditional).

Tools: Functions the LLM can call (search notes, create notes, web browse).

```python
from langgraph.graph import StateGraph, END
from langchain_openai import ChatOpenAI
from typing import TypedDict, Annotated
import operator

class AgentState(TypedDict):
    messages: Annotated[list, operator.add]
    current_step: str

# Define tools the agent can use
tools = [
    search_notes,     # Semantic search over user notes
    create_note,      # Create a new note
    summarize_notes,  # Summarize a set of notes
    web_search,       # Search the internet
]

# Create the LLM with tool access
llm = ChatOpenAI(model="gpt-4o-mini").bind_tools(tools)

# Define graph nodes
def agent_node(state: AgentState) -> AgentState:
    """LLM decides what to do."""
    response = llm.invoke(state["messages"])
    return {"messages": [response]}

def tool_node(state: AgentState) -> AgentState:
    """Execute the tool the LLM chose."""
    last_msg = state["messages"][-1]
    results = execute_tools(last_msg.tool_calls)
    return {"messages": results}

def should_continue(state: AgentState) -> str:
    """Route: if LLM used tools, go to tool node. Else, end."""
    last_msg = state["messages"][-1]
    if last_msg.tool_calls:
        return "tools"
    return END

# Build the graph
graph = StateGraph(AgentState)
graph.add_node("agent", agent_node)
graph.add_node("tools", tool_node)
graph.set_entry_point("agent")
graph.add_conditional_edges("agent", should_continue,
    {"tools": "tools", END: END})
graph.add_edge("tools", "agent")  # After tools, back to agent

app = graph.compile()
```

> **💡 The Loop**
>
> Notice the cycle: agent → tools → agent. The agent reasons, calls tools, observes results, reasons again, and either calls more tools or provides a final answer. This loop is the heart of agentic behavior.

## Reading: Defining Tools

A tool is a Python function with a clear docstring (the LLM reads it to decide when to use the tool) and type hints:

```python
from langchain_core.tools import tool

@tool
def search_notes(query: str) -> str:
    """Search the user's notes by meaning. Use when the user asks
    about their notes, wants to find information they saved, or
    references past entries."""
    results = vector_store.search(query, n_results=3)
    return format_results(results)

@tool
def create_note(title: str, content: str, tags: str = "") -> str:
    """Create a new note. Use when the user wants to save
    information, bookmark something, or record a thought."""
    tag_list = [t.strip() for t in tags.split(",") if t.strip()]
    note = note_store.add(title, content, tag_list)
    return f"Created note: {note.title}"
```

The docstring is critical — it is the LLM’s documentation for when and how to use the tool. Write it like you are explaining it to a smart colleague.

## Build: Nexus Agent

Create tools for: search_notes, create_note, list_recent_notes, summarize_topic, web_search. Build the LangGraph agent. Add a /agent endpoint that accepts natural language instructions and returns the agent’s final response plus a trace of what tools it used.

Example interactions the agent should handle:

"What did I write about machine learning last week?" → searches notes with date filter

"Save this article about React hooks" → creates a note with content

"Summarize all my notes about the Python project" → retrieves, then summarizes

"Find recent news about AI regulation and save the key points" → web search, then create note

## Review: Days 53–56

**Daily Checklist**

- [ ] I understand chatbot vs agent distinction
- [ ] I know the ReAct pattern (Reason, Act, Observe)
- [ ] I can define tools with docstrings the LLM reads
- [ ] I understand state machines and graph-based workflows
- [ ] I can build a multi-step agent with LangGraph
- [ ] Nexus agent can search, create, and summarize autonomously

> **🧠 Quiz**
>
> **Q1: What makes an agent different from a chatbot?**
> A) Better language
> B) Can take actions and use tools
> C) Faster responses
> D) More parameters
> ✅ Answer: B — Agents reason and act; chatbots just respond.
>
> **Q2: In a LangGraph agent, what happens after a tool runs?**
> A) Agent stops
> B) Result goes back to agent for more reasoning
> C) User is notified
> D) Database updates
> ✅ Answer: B — The agent loop: reason → act → observe → reason again.
>
> **Q3: Why is a tool's docstring important?**
> A) Python requires it
> B) The LLM reads it to decide when to use the tool
> C) It generates documentation
> D) It's optional
> ✅ Answer: B — The LLM uses the docstring to select the right tool.
>

# Days 57–60: Task Queues and Background Jobs

Handle long-running tasks without blocking users. Add Celery and Redis. (12 hours)

## Reading: Why Task Queues?

Some tasks take too long for a web request: sending 100 emails, processing large files, generating AI summaries of your entire note collection, scraping 50 web pages. If you do these in the API handler, the user waits (or the request times out).

Task queues solve this: the API enqueues a job and immediately responds with a task ID. A background worker picks up the job and processes it asynchronously. The user can check status anytime.

### The Architecture

Producer (your API) creates a message describing the task. Broker (Redis) holds the message queue. Consumer/Worker (Celery) picks up and executes tasks. Result Backend (Redis) stores task results.

This is the producer-consumer pattern — one of the most important patterns in distributed systems.

## Reading: Redis

Redis is an in-memory data store. It is blazing fast because data lives in RAM, not on disk. Used for: message queues, caching, session storage, rate limiting, and real-time features.

```yaml
# Add Redis to docker-compose.yml
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
```

## Reading: Celery

Celery is Python’s most popular distributed task queue. Install:

```bash
pip install celery[redis]
```

```python
# celery_app.py
from celery import Celery

celery = Celery(
    "nexus",
    broker="redis://localhost:6379/0",
    backend="redis://localhost:6379/1"
)

@celery.task
def generate_weekly_summary(user_id: int):
    """Background task: summarize this week's notes."""
    notes = get_notes_from_last_week(user_id)
    summary = llm_summarize(notes)
    save_summary(user_id, summary)
    send_email(user_id, subject="Weekly Summary", body=summary)
    return {"status": "complete", "summary_length": len(summary)}

@celery.task
def scrape_and_save(user_id: int, url: str):
    """Fetch a web page and save as a note."""
    content = fetch_page_content(url)
    cleaned = extract_main_text(content)
    create_note(user_id, title=f"Saved: {url}", content=cleaned)
    return {"status": "saved", "chars": len(cleaned)}
```

### Calling Tasks from the API

```python
from celery_app import generate_weekly_summary, scrape_and_save

@app.post("/tasks/weekly-summary")
async def request_summary(user_id: int):
    task = generate_weekly_summary.delay(user_id)  # .delay() = async
    return {"task_id": task.id, "status": "queued"}

@app.get("/tasks/{task_id}")
async def check_task(task_id: str):
    from celery.result import AsyncResult
    result = AsyncResult(task_id)
    return {"task_id": task_id, "status": result.status,
            "result": result.result if result.ready() else None}
```

Run the worker: celery -A celery_app worker --loglevel=info

## Build: Background Processing for Nexus

Add to docker-compose: Redis service and a Celery worker service. Create tasks: weekly summary generation, bulk import from uploaded files, web scraping to auto-create notes, email digest delivery. Add /tasks endpoints for submitting and monitoring.

## Review: Days 57–60

**Daily Checklist**

- [ ] I understand why task queues exist (long tasks, user not blocking)
- [ ] I know the producer-consumer pattern
- [ ] I can set up Celery with Redis as broker
- [ ] I can create, submit, and monitor background tasks
- [ ] Nexus processes long tasks without blocking the API

> **🧠 Quiz**
>
> **Q1: What does .delay() do on a Celery task?**
> A) Slows it down
> B) Sends it to the queue for async execution
> C) Delays by 5 seconds
> D) Runs it synchronously
> ✅ Answer: B — .delay() enqueues the task for background execution.
>
> **Q2: What role does Redis play here?**
> A) Database
> B) Message broker holding the task queue
> C) Web server
> D) Authentication
> ✅ Answer: B — Redis holds messages between producer (API) and consumer (worker).
>

# Days 61–65: Web Browsing, Email, and Deployment

Final sessions: Nexus browses the web, sends emails, and goes live. (15 hours)

## Reading: Web Scraping

Nexus’s agent needs to browse the web to fetch content. Python’s ecosystem:

httpx: async HTTP client for fetching pages. BeautifulSoup: HTML parsing and content extraction. playwright: headless browser for JavaScript-heavy sites.

```python
import httpx
from bs4 import BeautifulSoup

async def fetch_page_content(url: str) -> str:
    async with httpx.AsyncClient() as client:
        resp = await client.get(url, follow_redirects=True)
        resp.raise_for_status()
    soup = BeautifulSoup(resp.text, "html.parser")
    # Remove scripts, styles, nav
    for tag in soup(["script", "style", "nav", "footer"]):
        tag.decompose()
    return soup.get_text(separator="\n", strip=True)
```

## Reading: Sending Email

Use SMTP or an email service API (SendGrid, Resend):

```python
import smtplib
from email.mime.text import MIMEText

def send_email(to: str, subject: str, body: str):
    msg = MIMEText(body)
    msg["Subject"] = subject
    msg["From"] = os.getenv("EMAIL_FROM")
    msg["To"] = to
    with smtplib.SMTP(os.getenv("SMTP_HOST"), 587) as server:
        server.starttls()
        server.login(os.getenv("EMAIL_USER"),
                      os.getenv("EMAIL_PASS"))
        server.send_message(msg)
```

## Reading: Webhooks

Webhooks are HTTP callbacks: when an event happens in another system, it sends an HTTP POST to your API. This is event-driven architecture.

```python
@app.post("/webhooks/github")
async def github_webhook(payload: dict):
    """When a GitHub issue is created, save it as a note."""
    if payload.get("action") == "opened":
        issue = payload["issue"]
        create_note(
            title=f"GitHub: {issue['title']}",
            content=issue["body"],
            tags=["github", "issue"]
        )
    return {"status": "received"}
```

## Reading: Deployment

Your entire stack needs to run in the cloud. Options:

VPS (Digital Ocean, Hetzner): cheapest. You manage everything. Docker Compose works directly.

PaaS (Railway, Render, Fly.io): managed. Push code, they handle infrastructure.

Container orchestration (Kubernetes): for large scale. Overkill for most projects, but good to understand conceptually.

### Deployment Checklist

Production-ready means: HTTPS enabled (use Let’s Encrypt or Cloudflare), environment variables for all secrets, database backups configured, health check endpoint (/health), structured logging (JSON format), error monitoring (Sentry), rate limiting on all endpoints, CORS configured for your frontend domain only.

## Build: Final Nexus

Add web browsing tool to the agent. Add email sending as a background task. Create webhook endpoints for external integrations. Deploy the full stack: API + PostgreSQL + Redis + Celery worker + ChromaDB. Set up HTTPS, monitoring, and backups.

Your final docker-compose.yml should have:

```
services:
  api:        # FastAPI application
  db:         # PostgreSQL
  redis:      # Message broker + cache
  worker:     # Celery background worker
  chroma:     # Vector database
```

## Review: Days 61–65

**Daily Checklist**

- [ ] Nexus agent can browse the web and save content
- [ ] Nexus can send email notifications and digests
- [ ] I understand webhooks and event-driven architecture
- [ ] I can deploy a multi-service stack to the cloud
- [ ] HTTPS, monitoring, backups are configured
- [ ] The complete Nexus system is live in production

> **🧠 Quiz**
>
> **Q1: What is a webhook?**
> A) A JavaScript hook
> B) An HTTP callback triggered by external events
> C) A database trigger
> D) A CLI command
> ✅ Answer: B — External system sends HTTP POST to your API.
>
> **Q2: What is the minimum for production deployment?**
> A) Just push code
> B) HTTPS, env vars, backups, monitoring, rate limiting
> C) Kubernetes
> D) Only Docker
> ✅ Answer: B — Production requires security, reliability, and observability.
>

# Phase 4 Complete: What You Have Built

**Daily Checklist**

- [ ] AI agent with LangGraph that reasons and uses tools
- [ ] Background job processing with Celery + Redis
- [ ] Web scraping and content extraction
- [ ] Email notifications and digests
- [ ] Webhook integrations with external services
- [ ] Full-stack cloud deployment with 5 services
- [ ] Production-grade security, monitoring, and reliability

Your GitHub repository now has 100–150+ commits spanning 65 days. It tells the complete story of a developer who started with print("hello") and built a distributed AI agent system. This is exactly what hiring managers want to see.

# The Complete Nexus Journey

| Phase | Nexus Becomes       | Key Skills                       | Days  |
| ----- | ------------------- | -------------------------------- | ----- |
| 1     | CLI Note Manager    | Python, Git, Testing, OOP        | 1–18  |
| 2     | REST API + Database | HTTP, SQL, Async, Docker, Auth   | 19–38 |
| 3     | AI-Powered Brain    | Embeddings, RAG, LLMs, Streaming | 39–52 |
| 4     | Autonomous Agent    | LangGraph, Celery, Deployment    | 53–65 |

Total: 65 days × 3 hours = 195 hours of focused, structured learning.

> **📚 Concepts to Deepen on Your Own**
>
> • Multi-agent systems (agents coordinating with each other)
> • Long-term memory for agents (persisting across sessions)
> • Human-in-the-loop agent workflows
> • Kubernetes basics: pods, services, deployments, scaling
> • CI/CD pipelines with GitHub Actions
> • Infrastructure as Code (Terraform, Pulumi)
> • Observability: structured logging, distributed tracing (OpenTelemetry)
> • Message queues beyond Celery: RabbitMQ, Kafka, SQS
> • Event sourcing and CQRS patterns
> • Microservices vs monolith tradeoffs
> • Security: OWASP Top 10, penetration testing basics
> • Cost optimization for LLM APIs (caching, batching, model routing)
> • Evals and testing for AI systems
> • Responsible AI: bias detection, safety filters, guardrails
> • System design interview preparation using your Nexus architecture
