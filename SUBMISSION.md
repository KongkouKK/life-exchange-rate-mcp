# Life Exchange Rate MCP — submission draft

## Capability

Life Exchange Rate is a personal macro-impact engine. It separates macro headlines from structured numeric events, ranks events by market significance and user relevance, and translates selected events into travel purchasing power, rate/fuel scenarios, work time, and user-defined everyday units.

## API

- Base URL: `TO_BE_FILLED_AFTER_DEPLOY`
- Health: `TO_BE_FILLED_AFTER_DEPLOY/health`
- OpenAPI: `TO_BE_FILLED_AFTER_DEPLOY/openapi.json`
- MCP: `TO_BE_FILLED_AFTER_DEPLOY/mcp/`
- Verification proof: `TO_BE_FILLED_AFTER_DEPLOY/.well-known/xagent-verification.json`

## Review commit

`TO_BE_FILLED_AFTER_DEPLOY`

## Build and run

See `verification/README.md`, `README.md`, and `source/`.

## Data and security notes

- The core personal-impact calculations are deterministic; an LLM does not generate the arithmetic.
- User exposure values are request inputs and are not persisted by the MVP.
- Official Fed/ECB RSS is used as headline-trigger data only; headlines are never treated as numeric evidence.
- Live FX uses the dedicated Frankfurter ECB route with rolling anomaly scores; policy rates use official ECB SDMX daily observations.
- EIA_API_KEY, if supplied, is an environment variable only. Missing-key judging uses labeled synthetic fixtures.
- Policy-rate calculations enforce a currency-scope guardrail.
- Pass-through assumptions are returned explicitly and scenario estimates are not presented as forecasts or financial advice.
