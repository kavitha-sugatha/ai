# AI — Agentic AI Intro

Hands-on intro to **Agentic AI** with LangChain + LangGraph: build a ReAct agent
(`Reason → Act → Observe → Repeat`), give it tools, add memory, and discover
remote tools via MCP.

Start here: [`AgenticAI/agentic_AI_intro.ipynb`](AgenticAI/agentic_AI_intro.ipynb)

## What the notebook builds

| Step | What happens |
| ---- | ------------ |
| 1. Env setup | Loads `OPENAI_API_KEY` / `OPENAI_API_BASE` from `.env` via `python-dotenv` |
| 2. LLM setup | Creates `ChatOpenAI(model=gpt-4o-mini, temperature=0)` against a custom base URL |
| 3. Dummy email tool | `@tool def dummy_email_send(to, subject, body)` — prints to console, shows how agents gain capabilities |
| 4. ReAct agent | `create_react_agent(model=llm, tools=tools)` from `langgraph.prebuilt`; inspects `human / ai / tool` message history and `tool_calls` |
| 5. Web search tool | `web_search(query, max_results=5)` via DuckDuckGo (`ddgs`) — multi-step demo: search top Italian restaurant in Austin → pick one (e.g. Red Ash) → send email in one call |
| 6. Memory | `MemorySaver` checkpointer + `thread_id` — remembers "favorite cuisine is Japanese, dinner at 7:15 pm", then suggests Soto at 7:15 pm on the next request |
| 7. MCP demo | `MultiServerMCPClient` (SSE, `everything` server) for dynamic tool discovery, with graceful fallback to local tools if unreachable |
| 8. Code execution | `PythonREPLTool` agent for math reasoning (compound-interest example: `$450 → $603 in 6y ⇒ ~$498.15 in 2y`) |

## Repo layout

```text
.
├── README.md
├── .gitignore                  # excludes .env, config.json, .venv/, .DS_Store, checkpoints
└── AgenticAI/
    ├── agentic_AI_intro.ipynb  # the full demo
    └── .env.example            # copy to .env and fill in (never commit real keys)
```

> `AgenticAI/.env`, `AgenticAI/config.json`, `AgenticAI/.venv/` and `.DS_Store`
> exist locally but are intentionally **not committed** (see `.gitignore`).

## Quickstart

Requires Python 3.14 (notebook kernel was `.venv (3.14.7)`).

```bash
python3 -m venv .venv && source .venv/bin/activate

pip install langchain-openai==0.3.30 \
  langchain-core==0.3.74 \
  langchain-tools==0.1.34 \
  langgraph==0.6.6 \
  ddgs==9.5.4 \
  "primp==0.15.0" \
  langchain-mcp==0.2.1 \
  langchain-mcp-adapters==0.1.9 \
  nest_asyncio==1.6.0 \
  python-dotenv \
  langchain-experimental==0.3.4
```

Configure credentials:

```bash
cp AgenticAI/.env.example AgenticAI/.env
# edit AgenticAI/.env:
#   OPENAI_API_KEY=gl-...
#   OPENAI_API_BASE=https://aibe.mygreatlearning.com/openai/v1
#   # optional: OPENAI_MODEL_NAME=gpt-4o-mini
```

Run:

```bash
jupyter lab AgenticAI/agentic_AI_intro.ipynb
# then Run All — cells are ordered top-to-bottom
```

## Key snippets

ReAct agent:

```python
from langgraph.prebuilt import create_react_agent

agent = create_react_agent(model=llm, tools=tools)
result = agent.invoke({"messages": [user_msg]})
print(result["messages"][-1].content)
```

Memory (persists preferences across turns):

```python
from langgraph.checkpoint.memory import MemorySaver

checkpointer = MemorySaver()
agent = create_react_agent(model=llm, tools=tools, checkpointer=checkpointer)
cfg = {"configurable": {"thread_id": "thread1"}}
result = agent.invoke({"messages": [...]}, config={**cfg, "recursion_limit": 50})
```

MCP dynamic tools (falls back to local tools offline):

```python
from langchain_mcp_adapters.client import MultiServerMCPClient

mcp_client = MultiServerMCPClient({"everything": {
    "transport": "sse",
    "url": "https://everything.mcp.inevitable.fyi/sse",
}})
mcp_tools = await mcp_client.get_tools()
```

## Troubleshooting (from the notebook)

- **Python 3.14 + old langchain:** `from langchain.agents import ...` crashes on
  `Optional[dict[str, Any]]` — import `tool` from `langchain_core.tools` instead.
- **`ddgs` + `primp>=2`:** `firefox_117` impersonation is rejected — the notebook
  pins `primp==0.15.0` and only patches `HttpClient` when needed.
- **Don't call `nest_asyncio.apply()`** — it breaks `sniffio`/async detection for
  the OpenAI client; the notebook uses sync `agent.invoke()` with retries instead
  of `ainvoke`.
- **Transient 401s under load:** math/MCP cells retry up to 3× with backoff.
- **Colab vs VS Code:** Colab used Drive mount + Secrets; this repo uses local
  paths (`Path.cwd()`) + `.env`.

## Security

Never commit real keys. `.gitignore` covers `.env` and `config.json`.
Share only `.env.example`.
