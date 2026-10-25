---
name: "ui-live-check"
description: "Use to verify the Streamlit UI after a UI or agent-flow change: run the smoke test, then a short manual browser check in mock or live-LLM mode with a fixed checklist."
---

# UI live check

Unit tests run in one thread with fake components; they miss real-process bugs (threading, real embeddings, fake URLs). Always finish UI or agent-flow work with this check.

## 1. Automated (free)
- Run `python scripts/smoke_ui.py` (real Streamlit server, headless browser, mock mode, temp data dir). Never run it at the same time as the full pytest suite.
- It must pass every step before any manual check.

## 2. Manual browser check
Modes:
- Free: `streamlit run smartdesk_ai/ui/app.py` (mock answers; retrieval scores are not meaningful).
- Real answers, no real tickets (about $0.05): `SMARTDESK_LLM_MODE=live SMARTDESK_TICKETS_MODE=mock streamlit run smartdesk_ai/ui/app.py`. Start Redis first if the cache should be tested (`brew services start redis`).

Checklist (adapt to what changed):
1. Sidebar shows the expected mode.
2. KB question: answer with sources; debug panel shows dense cosine (vs the 0.40 threshold) and rerank scores.
3. Exact-term question (an error code): correct entry.
4. Same KB question twice: second is a cache hit and faster.
5. Compound question (one IT part, one HR part): one merged answer citing both domains.
6. Partial question (one answerable, one gap part): answer for the first, the gap named, ticket offer for that part only.
7. Ticket flow: draft, edit at the gate, yes; ticket number shown, no error.
8. New conversation, same email, status check: earlier ticket recalled.
9. Monitoring: no tracebacks; reranker ok; cost, cache, approval counters, agent metrics and cost-by-agent show numbers.
10. Ctrl+C to stop the server; stop Redis if not needed.

## 3. Report
- Anything wrong goes back as a concrete bug report (what you did, what you saw, any traceback), fixed test-first, then this check again.