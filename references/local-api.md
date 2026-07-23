# agentcents local API & CLI reference (Mode B)

Default base: `http://localhost:8082`. Everything is local; no auth.

## Detection

```bash
curl -sf -m 2 http://localhost:8082/health        # proxy running?
command -v agentcents                             # CLI installed?
```

If the CLI exists but the proxy isn't running, offer `agentcents start`
(foreground) before using HTTP endpoints; CLI reporting commands work
without the server.

## CLI

| command | what it gives you |
|---|---|
| `agentcents explain --tag T` (pipe a JSON request body on stdin) | per-candidate cost forecast, p50→p90, pick + unstable flag |
| `agentcents explain --accuracy [--hours N]` | predicted-vs-actual grading: p90 coverage (target ≈ 90%), q-error (1.00 = perfect) per (model, tag) |
| `agentcents usage` | spend by tag with budget remaining |
| `agentcents recent` | last calls: model, tokens, cost, latency |
| `agentcents rolling` | rolling 30-day spend |
| `agentcents models` / `agentcents sync` | catalog with live prices / refresh it |
| `agentcents local` | Ollama/local-model status and power-cost tracking |

## HTTP endpoints (read-only, JSON)

| endpoint | payload |
|---|---|
| `GET /health` | liveness |
| `GET /api/explain/accuracy?tag=&hours=` | accuracy groups + recent (forecast, actual) pairs |
| `GET /api/explain/histogram?tag=` | the output-token histogram the estimator runs on (p50/p90 markers) |
| `GET /api/plan?session_id=` | a session reconstructed as a DAG with per-node costs and counterfactuals |
| `GET /api/orchestrator` | per-node routing decisions with gate evaluations (synthetic showcase) |
| `GET /api/assistant/models` | installed local models + used-and-keyed cloud models |

## Proxying calls through it (for measuring, not required for consulting)

`POST /v1/chat/completions` (OpenAI shape) or `/v1/messages` (Anthropic
shape) with headers:

- `X-Agentcents-Target: <upstream base URL>` — e.g. `http://localhost:11434`
- `X-Agentcents-Tag: <task label>` — feeds per-tag histograms
- `X-Agentcents-Session: <id>` — groups calls into a plan
- `X-Agentcents-Cache: on|off|exact` — exact-match cache control

Budget enforcement: when a configured budget is exceeded the proxy returns
**HTTP 429** — report it as the user's own budget working, not an error.

## Dashboard pages (mention, don't scrape)

`/dashboard` (spend), `/plan` (DAG), `/orchestrator` (routing gates),
`/explain` (calibration). All carry the "Two Cents" guide drawer.

## Rendering guidance

- `explain` output → Mermaid `graph TD` with one node per candidate,
  cost range + intelligence in the label; green border = pick, note
  `unstable — advisory` when flagged.
- `/api/plan` → Mermaid DAG mirroring node dependencies; per-node model +
  cost in labels; headline = naive total vs routed total.
- Accuracy → state coverage vs the 90% target and q-error vs 1.00 in one
  sentence before any table.
