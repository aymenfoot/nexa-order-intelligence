# Nexa Order Intelligence

**Nexa Order Intelligence** is a production-grade ReAct AI agent for e-commerce customer support. It combines a LangGraph agent, a FastAPI gateway, a Streamlit chat UI, SQLite storage, and optional Langfuse tracing to handle order lookups, shipment tracking, and return workflows end-to-end.

Part of the **Nexa** portfolio.

## Highlights

- 7 tools: order lookup, email search, product search, shipment tracking, return policy, return eligibility, return initiation.
- ReAct loop with conditional routing and up to 3 automatic retries on tool errors.
- English-only responses with a fallback detector for non-English leakage.
- SQLite storage with 12 seeded orders and 9 shipments (auto-created on first run).
- Langfuse tracing for every LLM call, tool execution, latency, token count, and cost (optional).
- 24 unit tests covering services, tools, and graph logic — all pass without LLM credentials.
- Single command to start API + UI + open browser.

## Architecture

- **Chat UI:** Streamlit (customer-facing, quick-actions, "Details" expander)
- **Gateway:** FastAPI + Pydantic v2 (`POST /api/v1/chat`, async, typed, CORS-enabled)
- **Agent:** LangGraph `StateGraph` (ReAct loop with conditional routing and retry logic)
- **LLM:** GPT-4o-mini (OpenAI) via function calling — the LLM decides which tool to invoke
- **Storage:** SQLite (12 orders, 9 shipments, auto-seeded on first startup)
- **Observability:** Langfuse (optional; silently disabled without keys)

## Quick start

### Prerequisites

- Python 3.11+
- OpenAI API key (optional — services and tests run without it)
- (Optional) Langfuse account for tracing

### Setup

```bash
git clone https://github.com/aymenfoot/nexa-order-intelligence.git
cd nexa-order-intelligence
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
# Edit .env and add OPENAI_API_KEY=sk-... (optional)
```

### Run

```bash
python run.py
```

Opens:
- API → http://localhost:8000/docs (Swagger UI)
- Chat → http://localhost:8501 (Streamlit)

### Test

```bash
pytest tests/ -v    # 24 tests, ~1 second
```

## API reference

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/v1/health` | Health check → `{"status":"ok","version":"1.0.0"}` |
| `POST` | `/api/v1/chat` | Send message → agent response |

Request:

```json
{ "message": "What is the status of ORD-1002?", "session_id": "optional" }
```

## Configuration

Environment variables (see `.env.example`):

| Variable | Description | Default |
|---|---|---|
| `OPENAI_API_KEY` | OpenAI API key (optional for services/tests) | — |
| `OPENAI_MODEL` | Model name | `gpt-4o-mini` |
| `LANGFUSE_PUBLIC_KEY` | Langfuse public key (optional) | — |
| `LANGFUSE_SECRET_KEY` | Langfuse secret key (optional) | — |

## Testing

```bash
pytest tests/ -v
```

24 tests covering services, tools, graph logic, and the API. All pass without LLM credentials.

## License

MIT License. See [LICENSE](LICENSE).
