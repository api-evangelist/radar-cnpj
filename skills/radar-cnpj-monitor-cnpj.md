---
name: radar-cnpj-monitor-cnpj
description: >-
  Monitor Brazilian companies for registration changes using Radar CNPJ's anonymous sessions —
  10 free watches, x402 micropayments beyond that.
api: Radar CNPJ API
generated: '2026-09-05'
method: generated
source: openapi/radar-cnpj-openapi.json + https://radar-cnpj.com/llms.txt
operations:
  - monitor_session
  - put_api_monitor_session_email
  - add_watch
  - list_watches
  - get_api_me_monitor_alerts
  - get_api_monitor_changes_by_cnpj
  - delete_api_me_monitor_watch_by_cnpj
---

# Monitor CNPJs for registration changes

Base URL: `https://radar-cnpj.com`. There are no accounts: a session uuid is the credential.
Treat it as a secret — whoever holds it owns the session.

1. **Create a session** — `POST /api/monitor/session` (operationId `monitor_session`). Returns
   `session_id`; send it as the `x-radar-session` header on every step below. A 401 anywhere
   means the header is absent or unknown.
2. **Register an alert email (optional)** — `PUT /api/monitor/session/email`
   (operationId `put_api_monitor_session_email`) with `{"email": "..."}`.
3. **Watch a company** — `POST /api/me/monitor/watch` (operationId `add_watch`) with the
   14-digit `cnpj`. The first 10 watches per session are free; beyond that the API answers
   HTTP 402 with x402 `accepts[]` (USDC on Base, $0.50 per CNPJ per 30 days) — pay and repeat
   the same call with the `X-PAYMENT` header.
4. **Check quota and watches** — `GET /api/me/monitor/watches` (operationId `list_watches`)
   returns `watches`, `plan`, `quota`, `used`.
5. **Read alerts** — `GET /api/me/monitor/alerts` (operationId `get_api_me_monitor_alerts`).
6. **Read a company's full history** — `GET /api/monitor/changes/{cnpj}`
   (operationId `get_api_monitor_changes_by_cnpj`).
7. **Stop watching (reversal)** — `DELETE /api/me/monitor/watch/{cnpj}`
   (operationId `delete_api_me_monitor_watch_by_cnpj`). The key is the CNPJ itself, not an id.
   No refund of a paid watch is documented — treat the $0.50 as spent.
