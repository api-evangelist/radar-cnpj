---
name: radar-cnpj-evaluate-business-idea
description: >-
  Evaluate a business idea against the Brazilian Receita Federal registry, then drill into the
  companies already operating in that space and export the list.
api: Radar CNPJ API
generated: '2026-09-05'
method: generated
source: openapi/radar-cnpj-openapi.json + https://radar-cnpj.com/llms.txt
operations:
  - avaliar
  - ia_filters
  - search
  - get_api_export
  - ref
  - get_cnpj
---

# Evaluate a business idea with Radar CNPJ

All steps are anonymous and free. Base URL: `https://radar-cnpj.com`. Errors arrive as HTTP 4xx
with a `{ ok: false, code, error }` body — never `ok:false` on a 200.

1. **Evaluate the idea** — `POST /api/avaliar` (operationId `avaliar`) with
   `{"texto": "<idea in plain text, 3-400 chars>"}` (optional `uf`, `municipio`). Returns the
   mapped CNAE, normalized filters, and a formal-offer profile (`ficha`) with counts of
   companies already doing this. 400 means the text is outside 3-400 characters.
2. **Refine to filters (optional)** — `POST /api/ia` (operationId `ia_filters`) turns free text
   into the normalized filters the search accepts. On a 504, enqueue the same request via
   `POST /api/ia/jobs` and poll `GET /api/ia/jobs/{id}`.
3. **List the companies** — `GET /api/busca` (operationId `search`) with `q` and/or `f` (JSON
   filters), paginated with `page`/`pageSize`; the response carries `hasMore`. A 400 with code
   `busca_vazia` means no term and no filter was sent.
4. **Export the list** — `GET /api/export` (operationId `get_api_export`) with the same filters,
   `formato=csv|json`. The response is capped and says so (`capped`).
5. **Look up vocabularies** — `GET /api/ref` (operationId `ref`, `tipo=cnae|municipio|natureza`)
   for official CNAE/municipality/natureza juridica codes to build filters.
6. **Inspect one company** — `GET /api/cnpj/{cnpj}` (operationId `get_cnpj`) with the 14-digit
   CNPJ. Edge-cached 6 hours; check data age via `GET /api/health` (`import.dump_date`).
