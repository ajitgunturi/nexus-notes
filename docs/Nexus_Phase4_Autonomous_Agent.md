# Phase 4: Autonomous Agent

> **Phase 4 of 4** | Duration: 13 days (~39 hours) | Prerequisites: Phase 3 complete

In Phase 3, Nexus answered questions. In Phase 4, it takes actions. The difference is autonomy: instead of just retrieving and responding, Nexus can browse the web, send emails, schedule tasks, and make decisions about which tools to use — all without step-by-step instructions from you.

This is the frontier of modern AI engineering: building systems that can reason, plan, and act. By the end of Phase 4, Nexus is a distributed, autonomous agent system deployed to the cloud.

Each session follows: Reading → Build → Review (checklist + quiz) → Exercises → Further Reading.

---

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

### Quiz

**Q1: What makes an agent different from a chatbot?**

- A) Better language
- B) Can take actions and use tools
- C) Faster responses
- D) More parameters

<details><summary>Answer</summary>

**B** — Agents reason and act; chatbots just respond.

</details>

**Q2: In a LangGraph agent, what happens after a tool runs?**

- A) Agent stops
- B) Result goes back to agent for more reasoning
- C) User is notified
- D) Database updates

<details><summary>Answer</summary>

**B** — The agent loop: reason → act → observe → reason again.

</details>

**Q3: Why is a tool's docstring important?**

- A) Python requires it
- B) The LLM reads it to decide when to use the tool
- C) It generates documentation
- D) It's optional

<details><summary>Answer</summary>

**B** — The LLM uses the docstring to select the right tool.

</details>

**Q4: In a LangGraph state machine, what is the role of a conditional edge?**

- A) It connects two nodes with a fixed path
- B) It routes the graph to different nodes based on the current state
- C) It deletes nodes that are no longer needed
- D) It stores tool results in the database

<details><summary>Answer</summary>

**B** — Conditional edges evaluate the current state (e.g., did the LLM call a tool?) and route execution to the appropriate next node, enabling dynamic branching within the agent loop.

</details>

## Exercises

**Exercise 1 (Easy): Define a LangGraph-compatible tool**

Write a `@tool`-decorated function called `list_recent_notes` that accepts an integer `days` (default 7) and returns a formatted string listing note titles from the last N days. Include a docstring that clearly explains when the LLM should call this tool.

<details><summary>Solution</summary>

```python
from langchain_core.tools import tool
from datetime import datetime, timedelta

@tool
def list_recent_notes(days: int = 7) -> str:
    """List the titles of notes created in the last N days.
    Use when the user asks what they have been working on recently,
    wants a summary of recent activity, or asks about notes from
    the past week or a specific number of days."""
    cutoff = datetime.utcnow() - timedelta(days=days)
    notes = note_store.query(created_after=cutoff)
    if not notes:
        return f"No notes found in the last {days} days."
    lines = [f"- {note.title} ({note.created_at.date()})" for note in notes]
    return f"Notes from the last {days} days:\n" + "\n".join(lines)
```

The docstring explains the *when*, not the *how*. The default argument makes it flexible. The return value is a plain string the LLM can read as an observation.

</details>

---

**Exercise 2 (Medium): Build a minimal ReAct agent loop with LangGraph**

Implement a LangGraph `StateGraph` that wires together an `agent_node` (calls an LLM with bound tools) and a `tool_node` (executes tool calls). Use a `should_continue` conditional edge. The graph should cycle agent → tools → agent until the LLM produces a message with no tool calls, then terminate. Include the `AgentState` TypedDict and `graph.compile()` call.

<details><summary>Solution</summary>

```python
from langgraph.graph import StateGraph, END
from langchain_openai import ChatOpenAI
from langchain_core.messages import ToolMessage, HumanMessage
from typing import TypedDict, Annotated
import operator

class AgentState(TypedDict):
    messages: Annotated[list, operator.add]

tools = [list_recent_notes, search_notes, create_note]
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0).bind_tools(tools)
tool_map = {t.name: t for t in tools}

def agent_node(state: AgentState) -> AgentState:
    """Call the LLM and append its response to messages."""
    response = llm.invoke(state["messages"])
    return {"messages": [response]}

def tool_node(state: AgentState) -> AgentState:
    """Execute every tool call in the last AI message."""
    last_msg = state["messages"][-1]
    results = []
    for call in last_msg.tool_calls:
        fn = tool_map[call["name"]]
        output = fn.invoke(call["args"])
        results.append(ToolMessage(content=str(output), tool_call_id=call["id"]))
    return {"messages": results}

def should_continue(state: AgentState) -> str:
    last_msg = state["messages"][-1]
    if getattr(last_msg, "tool_calls", None):
        return "tools"
    return END

graph = StateGraph(AgentState)
graph.add_node("agent", agent_node)
graph.add_node("tools", tool_node)
graph.set_entry_point("agent")
graph.add_conditional_edges("agent", should_continue, {"tools": "tools", END: END})
graph.add_edge("tools", "agent")
app = graph.compile()

result = app.invoke({"messages": [HumanMessage(content="What notes did I add this week?")]})
print(result["messages"][-1].content)
```

</details>

---

**Exercise 3 (Challenge): Add a FastAPI `/agent` endpoint with tool-use trace**

Wrap the compiled LangGraph agent in a FastAPI `POST /agent` endpoint. The endpoint should accept `{"instruction": "..."}`, run the agent, and return both the final answer and a structured trace of every tool call made during the run (tool name, inputs, output). Use Pydantic response models.

<details><summary>Solution</summary>

```python
from fastapi import FastAPI
from pydantic import BaseModel
from langchain_core.messages import HumanMessage, AIMessage, ToolMessage
from typing import Any

app_api = FastAPI()

class AgentRequest(BaseModel):
    instruction: str

class ToolCall(BaseModel):
    tool_name: str
    inputs: dict[str, Any]
    output: str

class AgentResponse(BaseModel):
    answer: str
    tool_trace: list[ToolCall]

@app_api.post("/agent", response_model=AgentResponse)
async def run_agent(request: AgentRequest):
    initial_state = {"messages": [HumanMessage(content=request.instruction)]}
    final_state = app.invoke(initial_state)  # 'app' is the compiled LangGraph

    messages = final_state["messages"]
    trace: list[ToolCall] = []
    tool_outputs: dict[str, str] = {}

    # Index ToolMessages by tool_call_id for O(1) lookup
    for msg in messages:
        if isinstance(msg, ToolMessage):
            tool_outputs[msg.tool_call_id] = msg.content

    # Match AI tool_calls to their ToolMessage outputs
    for msg in messages:
        if isinstance(msg, AIMessage) and getattr(msg, "tool_calls", None):
            for call in msg.tool_calls:
                trace.append(ToolCall(
                    tool_name=call["name"],
                    inputs=call["args"],
                    output=tool_outputs.get(call["id"], ""),
                ))

    final_answer = messages[-1].content
    return AgentResponse(answer=final_answer, tool_trace=trace)
```

This pattern separates concerns cleanly: the graph handles reasoning, the endpoint handles HTTP, and the Pydantic models enforce the response contract. The trace lets the frontend show users exactly what the agent did and why.

</details>

## Further Reading

- [LangGraph: Build Stateful Agents — Official Documentation](https://langchain-ai.github.io/langgraph/) — The authoritative guide to LangGraph's `StateGraph`, node design, conditional edges, and persistence. Work through the "ReAct agent from scratch" tutorial to reinforce everything built in this session.
- [ReAct: Synergizing Reasoning and Acting in Language Models (arXiv:2210.03629)](https://arxiv.org/abs/2210.03629) — The original paper that introduced the ReAct pattern. Reading the abstract and Section 2 takes 20 minutes and gives you a concrete mental model of why Thought / Action / Observation loops outperform pure reasoning or pure action chains.


---

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

### Quiz

**Q1: What does .delay() do on a Celery task?**

- A) Slows it down
- B) Sends it to the queue for async execution
- C) Delays by 5 seconds
- D) Runs it synchronously

<details><summary>Answer</summary>

**B** — .delay() enqueues the task for background execution.

</details>

**Q2: What role does Redis play here?**

- A) Database
- B) Message broker holding the task queue
- C) Web server
- D) Authentication

<details><summary>Answer</summary>

**B** — Redis holds messages between producer (API) and consumer (worker).

</details>

**Q3: In the producer-consumer pattern, what happens if the worker crashes mid-task?**

- A) The task result is returned immediately
- B) The message is requeued by the broker so another worker can retry it
- C) The API automatically retries the HTTP request
- D) Redis deletes the task permanently

<details><summary>Answer</summary>

**B** — Celery uses message acknowledgement: a task is only removed from the queue after the worker confirms success. If the worker crashes before acknowledging, the broker requeues the message for another worker to pick up, providing fault tolerance.

</details>

**Q4: Which Celery feature prevents a task from being processed more than once per time window?**

- A) `.delay()`
- B) `task_soft_time_limit`
- C) `task_acks_late` with idempotency keys
- D) `rate_limit` on the task decorator

<details><summary>Answer</summary>

**D** — Setting `rate_limit` on a `@celery.task(rate_limit="10/m")` tells the worker to process at most 10 executions of that task per minute. For true deduplication, combine this with idempotency keys stored in Redis.

</details>

## Exercises

**Exercise 1 (Easy): Define and enqueue a Celery task**

Write a Celery task called `send_digest_email` that accepts `user_id: int` and `note_ids: list[int]`. The task should fetch the notes, build a plain-text email body listing each note title and a one-sentence preview, and call `send_email()`. Show how to enqueue it with `.delay()` from a FastAPI endpoint `POST /tasks/digest`.

<details><summary>Solution</summary>

```python
# celery_app.py
from celery import Celery

celery = Celery(
    "nexus",
    broker="redis://localhost:6379/0",
    backend="redis://localhost:6379/1",
)

@celery.task(bind=True, max_retries=3)
def send_digest_email(self, user_id: int, note_ids: list[int]):
    """Fetch the requested notes and email a digest to the user."""
    try:
        user = get_user(user_id)
        notes = [get_note(nid) for nid in note_ids]
        lines = [
            f"- {note.title}: {note.content[:100].strip()}..."
            for note in notes
        ]
        body = "Your Nexus digest:\n\n" + "\n".join(lines)
        send_email(to=user.email, subject="Your Nexus Digest", body=body)
        return {"status": "sent", "note_count": len(notes)}
    except Exception as exc:
        raise self.retry(exc=exc, countdown=60)  # retry after 60 s


# main.py (FastAPI)
from fastapi import FastAPI
from pydantic import BaseModel
from celery_app import send_digest_email

api = FastAPI()

class DigestRequest(BaseModel):
    user_id: int
    note_ids: list[int]

@api.post("/tasks/digest")
async def request_digest(body: DigestRequest):
    task = send_digest_email.delay(body.user_id, body.note_ids)
    return {"task_id": task.id, "status": "queued"}
```

`bind=True` gives the task access to `self` so it can call `self.retry()`. Always set `max_retries` on tasks that call external services.

</details>

---

**Exercise 2 (Medium): Poll task status with a FastAPI endpoint**

Write a `GET /tasks/{task_id}` FastAPI endpoint that returns the task's current state (`PENDING`, `STARTED`, `SUCCESS`, `FAILURE`), the result when ready, and the traceback string if it failed. Use Celery's `AsyncResult`. Return a structured Pydantic response.

<details><summary>Solution</summary>

```python
from fastapi import FastAPI
from pydantic import BaseModel
from celery.result import AsyncResult
from typing import Any, Optional

api = FastAPI()

class TaskStatus(BaseModel):
    task_id: str
    state: str
    result: Optional[Any] = None
    error: Optional[str] = None

@api.get("/tasks/{task_id}", response_model=TaskStatus)
async def get_task_status(task_id: str):
    result = AsyncResult(task_id)

    if result.state == "PENDING":
        return TaskStatus(task_id=task_id, state="PENDING")
    if result.state == "STARTED":
        return TaskStatus(task_id=task_id, state="STARTED")
    if result.state == "SUCCESS":
        return TaskStatus(task_id=task_id, state="SUCCESS", result=result.result)
    if result.state == "FAILURE":
        return TaskStatus(task_id=task_id, state="FAILURE", error=str(result.result))
    # RETRY, REVOKED, or custom states
    return TaskStatus(task_id=task_id, state=result.state)
```

PENDING is the default state for unknown task IDs as well as genuinely pending tasks — there is no way to distinguish between "never submitted" and "queued but not started" without storing task IDs separately in PostgreSQL when the job is created.

</details>

---

**Exercise 3 (Challenge): Build a Celery workflow with a chord — parallel scrape then summarise**

Use a Celery `chord` to scrape a list of URLs in parallel (one `scrape_page` task per URL), then pass all results to a single `summarise_pages` callback task that calls the LLM to produce a combined summary. The FastAPI endpoint `POST /tasks/research` should accept `{"urls": [...], "user_id": int}` and return a task ID for the chord's final result.

<details><summary>Solution</summary>

```python
# celery_app.py
from celery import Celery, chord, group
import httpx
from bs4 import BeautifulSoup

celery = Celery("nexus",
                broker="redis://localhost:6379/0",
                backend="redis://localhost:6379/1")

@celery.task
def scrape_page(url: str) -> dict:
    """Fetch one URL and return its cleaned text."""
    with httpx.Client(timeout=15) as client:
        resp = client.get(url, follow_redirects=True)
        resp.raise_for_status()
    soup = BeautifulSoup(resp.text, "html.parser")
    for tag in soup(["script", "style", "nav", "footer"]):
        tag.decompose()
    text = soup.get_text(separator="\n", strip=True)
    return {"url": url, "text": text[:4000]}  # truncate for LLM context


@celery.task
def summarise_pages(pages: list[dict], user_id: int) -> dict:
    """Summarise all scraped pages and save as a note."""
    combined = "\n\n---\n\n".join(
        f"Source: {p['url']}\n{p['text']}" for p in pages
    )
    prompt = f"Summarise the key points from these web pages:\n\n{combined}"
    summary = llm_complete(prompt)
    note = create_note(
        user_id=user_id, title="Research Summary",
        content=summary, tags=["research", "auto-generated"],
    )
    return {"note_id": note.id, "summary": summary}


# main.py (FastAPI)
from fastapi import FastAPI
from pydantic import BaseModel
from celery import chord, group
from celery_app import scrape_page, summarise_pages

api = FastAPI()

class ResearchRequest(BaseModel):
    urls: list[str]
    user_id: int

@api.post("/tasks/research")
async def start_research(body: ResearchRequest):
    # chord: run scrape_page for every URL in parallel,
    # then call summarise_pages with all results.
    workflow = chord(
        group(scrape_page.s(url) for url in body.urls),
        summarise_pages.s(body.user_id),  # results injected as first arg
    )
    result = workflow.apply_async()
    return {"task_id": result.id, "url_count": len(body.urls), "status": "queued"}
```

`chord` guarantees that `summarise_pages` only runs after every parallel `scrape_page` task completes. The `.s()` method creates a Celery signature (a lazy task description) rather than executing immediately. The chord's `.id` is the ID of the callback task, which you poll via `GET /tasks/{task_id}`.

</details>

## Further Reading

- [Celery Documentation: Canvas — Designing Workflows](https://docs.celeryq.dev/en/stable/userguide/canvas.html) — The official guide to Celery primitives: `chain`, `group`, `chord`, and `chunks`. Understanding canvas is the difference between running one task at a time and building real distributed pipelines.
- [Redis Documentation: Data Types — Streams](https://redis.io/docs/latest/develop/data-types/streams/) — Redis Streams are a more durable alternative to Celery's list-based queue for high-throughput event pipelines. Understanding Streams alongside Celery gives you the vocabulary to compare message brokers intelligently in system design conversations.


---

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

```yaml
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

### Quiz

**Q1: What is a webhook?**

- A) A JavaScript hook
- B) An HTTP callback triggered by external events
- C) A database trigger
- D) A CLI command

<details><summary>Answer</summary>

**B** — External system sends HTTP POST to your API.

</details>

**Q2: What is the minimum for production deployment?**

- A) Just push code
- B) HTTPS, env vars, backups, monitoring, rate limiting
- C) Kubernetes
- D) Only Docker

<details><summary>Answer</summary>

**B** — Production requires security, reliability, and observability.

</details>

**Q3: When scraping a JavaScript-heavy site with httpx + BeautifulSoup, what problem will you encounter and how do you solve it?**

- A) The response is always empty; use a VPN instead
- B) httpx only fetches the initial HTML; JavaScript-rendered content is missing — use Playwright to run a real browser
- C) BeautifulSoup cannot parse HTML5; use lxml only
- D) httpx is synchronous and will block the event loop

<details><summary>Answer</summary>

**B** — `httpx` sends an HTTP request and receives the raw HTML the server sends before any JavaScript runs. Single-page apps (React, Vue, Angular) render their content client-side, so that raw HTML is nearly empty. Playwright (or Selenium) launches a real Chromium browser, waits for JavaScript to execute, and then lets you read the fully-rendered DOM.

</details>

**Q4: Which of the following is NOT a valid reason to use a webhook instead of polling?**

- A) You want to receive events in real time without repeatedly hitting the external API
- B) The external service does not offer a webhook API
- C) You want to reduce unnecessary HTTP requests when events are infrequent
- D) You want your server to receive pushes rather than pull data on a schedule

<details><summary>Answer</summary>

**B** — If the external service does not offer webhooks, you have no choice but to poll. All the other options are valid motivations for preferring webhooks: real-time delivery, reduced request overhead, and the push model. When webhooks are unavailable, a Celery periodic task (`celery beat`) polling on a schedule is the pragmatic fallback.

</details>

## Exercises

**Exercise 1 (Easy): Write an async web scraper function**

Implement an `async` function `fetch_article(url: str) -> dict` using `httpx.AsyncClient` and BeautifulSoup. It should strip `<script>`, `<style>`, `<nav>`, `<header>`, and `<footer>` tags, extract the page `<title>`, and return `{"title": ..., "text": ..., "url": ...}`. Limit the returned text to 5000 characters.

<details><summary>Solution</summary>

```python
import httpx
from bs4 import BeautifulSoup

STRIP_TAGS = ["script", "style", "nav", "header", "footer", "aside"]

async def fetch_article(url: str) -> dict:
    """Fetch a web page and return its title and cleaned text."""
    headers = {"User-Agent": "Mozilla/5.0 (compatible; NexusBot/1.0)"}
    async with httpx.AsyncClient(timeout=20, follow_redirects=True) as client:
        resp = await client.get(url, headers=headers)
        resp.raise_for_status()

    soup = BeautifulSoup(resp.text, "html.parser")

    # Extract title before stripping tags
    title = soup.title.string.strip() if soup.title else url

    for tag in soup(STRIP_TAGS):
        tag.decompose()

    text = soup.get_text(separator="\n", strip=True)
    lines = [ln for ln in text.splitlines() if ln.strip()]
    clean_text = "\n".join(lines)[:5000]

    return {"title": title, "text": clean_text, "url": url}
```

Always set a custom `User-Agent` header — many servers reject requests from `python-httpx/x.x.x`. Set a generous timeout (20 s) because slow servers will hang indefinitely without it.

</details>

---

**Exercise 2 (Medium): Create a secure webhook endpoint with signature verification**

Write a FastAPI `POST /webhooks/github` endpoint that: (1) reads the raw request body; (2) verifies the `X-Hub-Signature-256` HMAC header against a shared secret stored in `GITHUB_WEBHOOK_SECRET`; (3) rejects requests with an invalid signature with HTTP 401; (4) on valid `"issues"` events with `action == "opened"`, saves the issue as a Nexus note.

<details><summary>Solution</summary>

```python
import hashlib
import hmac
import os
from fastapi import FastAPI, Request, HTTPException, Header
from typing import Optional

api = FastAPI()

def verify_github_signature(body: bytes, signature_header: Optional[str]) -> bool:
    """Return True if the HMAC-SHA256 signature matches our secret."""
    if not signature_header:
        return False
    secret = os.getenv("GITHUB_WEBHOOK_SECRET", "").encode()
    expected = "sha256=" + hmac.new(secret, body, hashlib.sha256).hexdigest()
    # Use hmac.compare_digest to prevent timing attacks
    return hmac.compare_digest(expected, signature_header)

@api.post("/webhooks/github")
async def github_webhook(
    request: Request,
    x_hub_signature_256: Optional[str] = Header(default=None),
    x_github_event: Optional[str] = Header(default=None),
):
    body = await request.body()

    if not verify_github_signature(body, x_hub_signature_256):
        raise HTTPException(status_code=401, detail="Invalid signature")

    payload = await request.json()

    if x_github_event == "issues" and payload.get("action") == "opened":
        issue = payload["issue"]
        repo = payload["repository"]["full_name"]
        create_note(
            title=f"GitHub Issue: {issue['title']}",
            content=f"**Repo:** {repo}\n**URL:** {issue['html_url']}\n\n{issue.get('body', '')}",
            tags=["github", "issue", repo.split("/")[-1]],
        )
        return {"status": "saved", "issue": issue["number"]}

    return {"status": "ignored", "event": x_github_event}
```

`hmac.compare_digest` is mandatory for security: a naive `==` comparison leaks timing information that allows attackers to brute-force the secret one byte at a time. Reading the raw body before calling `request.json()` is also essential — once the body is consumed as JSON, you cannot re-read the bytes for HMAC verification.

</details>

---

**Exercise 3 (Challenge): Write a production `docker-compose.yml` with health checks and a Caddy HTTPS reverse proxy**

Write a complete `docker-compose.yml` for the full Nexus stack (API, PostgreSQL, Redis, Celery worker, ChromaDB) that includes: `healthcheck` blocks for every service, a Caddy service that terminates HTTPS automatically via Let's Encrypt, all secrets passed via environment variables, and `depends_on` with `condition: service_healthy`.

<details><summary>Solution</summary>

```yaml
# docker-compose.yml
version: "3.9"

services:

  caddy:
    image: caddy:2-alpine
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile:ro
      - caddy_data:/data
      - caddy_config:/config
    depends_on:
      api:
        condition: service_healthy

  api:
    build: .
    restart: unless-stopped
    environment:
      DATABASE_URL: postgresql+asyncpg://${POSTGRES_USER}:${POSTGRES_PASSWORD}@db:5432/${POSTGRES_DB}
      REDIS_URL: redis://redis:6379/0
      OPENAI_API_KEY: ${OPENAI_API_KEY}
      SECRET_KEY: ${SECRET_KEY}
      GITHUB_WEBHOOK_SECRET: ${GITHUB_WEBHOOK_SECRET}
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 20s
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_healthy

  worker:
    build: .
    command: celery -A celery_app worker --loglevel=info --concurrency=4
    restart: unless-stopped
    environment:
      DATABASE_URL: postgresql+asyncpg://${POSTGRES_USER}:${POSTGRES_PASSWORD}@db:5432/${POSTGRES_DB}
      REDIS_URL: redis://redis:6379/0
      OPENAI_API_KEY: ${OPENAI_API_KEY}
    healthcheck:
      test: ["CMD", "celery", "-A", "celery_app", "inspect", "ping"]
      interval: 30s
      timeout: 10s
      retries: 3
    depends_on:
      redis:
        condition: service_healthy

  db:
    image: postgres:16-alpine
    restart: unless-stopped
    environment:
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
      POSTGRES_DB: ${POSTGRES_DB}
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER} -d ${POSTGRES_DB}"]
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    restart: unless-stopped
    command: redis-server --appendonly yes
    volumes:
      - redis_data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5

  chroma:
    image: chromadb/chroma:latest
    restart: unless-stopped
    volumes:
      - chroma_data:/chroma/chroma
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/api/v1/heartbeat"]
      interval: 15s
      timeout: 5s
      retries: 5

volumes:
  postgres_data:
  redis_data:
  chroma_data:
  caddy_data:
  caddy_config:
```

```text
# Caddyfile
yourdomain.com {
    reverse_proxy api:8000
    encode gzip
    header {
        Strict-Transport-Security "max-age=31536000; includeSubDomains"
        X-Content-Type-Options nosniff
        X-Frame-Options DENY
    }
}
```

Three things make this production-grade: (1) `condition: service_healthy` ensures the API never starts against an unready database; (2) Caddy handles TLS certificate issuance and renewal automatically with zero configuration beyond the domain name; (3) `--appendonly yes` on Redis means the task queue survives a container restart without losing queued jobs.

</details>

## Further Reading

- [Caddy Documentation: Automatic HTTPS](https://caddyserver.com/docs/automatic-https) — Caddy issues and renews Let's Encrypt certificates automatically, requires no Certbot cron jobs, and its `Caddyfile` syntax is far simpler than Nginx. This page explains exactly how the certificate lifecycle works.
- [OWASP Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/) — Covers signature verification, SSRF risks from user-supplied webhook URLs, and replay attack prevention — all directly applicable to the GitHub webhook endpoint built in Exercise 2.


---

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

---

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
