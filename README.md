# Life Exchange Rate MCP

**Every headline has a price. See yours.**

Translate structured macroeconomic events into travel purchasing power, mortgage/savings and fuel scenarios, work time, and user-defined everyday units. Headlines trigger attention; **only structured numeric observations drive calculations**. Wages, budgets, prices and pass-through assumptions come from explicit inputs or clearly labeled demonstration defaults.

## Run locally

Requires Python 3.11+ and [uv](https://docs.astral.sh/uv/getting-started/installation/).

```powershell
uv sync --locked --extra dev
uv run --frozen python -m pytest
uv run --frozen uvicorn life_exchange_rate.main:app --app-dir source --host 127.0.0.1 --port 8000
```

Open [API docs](http://localhost:8000/docs), [health](http://localhost:8000/health), or [demo radar](http://localhost:8000/v1/radar/demo). The canonical MCP endpoint is **http://localhost:8000/mcp/**, including the trailing slash. It is a protocol endpoint, not a web page.

For configuration, copy `.env.example` to `.env` and add `--env-file .env` to the Uvicorn command. Environment files are not loaded automatically. Never commit API keys. Set `PUBLIC_BASE_URL` to the actual public origin when deploying; the MCP host allowlist derives from it. Both `/health` and `/.well-known/xagent-verification.json` read the same `REVIEW_COMMIT` and package version. `dev-local` is a development placeholder, not a review commit.

## End-to-end demo

```powershell
# Reproducible synthetic judging scenario, no network or key required:
uv run --frozen python -m life_exchange_rate.demo
# Actual ECB/Frankfurter observations -> ranking -> SEK/JPY travel -> work/life units:
uv run --frozen python -m life_exchange_rate.demo --live
# Real MCP HTTP client in current and legacy protocol modes:
uv run --frozen python verification/smoke_mcp.py --live-fx
```

The smoke script starts and stops a local server automatically. Without `--live-fx`, it checks the offline chain and explicitly records the live FX/RSS calls as skipped. Evidence is saved in `verification/mcp-smoke.json` and `verification/end-to-end-demo.json`.

In the fixed synthetic scenario, 20,000 SEK bought 300,000 JPY at 15 JPY/SEK and buys 276,000 JPY at 13.8. Restoring the original purchasing power needs **1,739.13 SEK**, equivalent to **8.70 work hours**, **38.65 coffees**, or **12.42 lunches** at the declared demonstration income and prices. These alternatives are equivalents of the same amount, not additional expenses. They are not current market data or the user's finances.

## Data and guarantees

- Live FX uses Frankfurter's dedicated ECB route, correct calendar lookback selection and a rolling log-return anomaly score. Every score reports its horizon, reference sample count, mean, sample volatility and fallback reason. See `docs/FX_ANOMALY.md`.
- Policy rates use the official ECB daily euro-area deposit facility series. Events apply only to matching EUR exposures.
- Energy uses EIA API v2 Brent daily spot prices. `EIA_API_KEY` is needed only for live energy. No configured key means a labeled synthetic fallback in `live_or_fixture` mode.
- Both structured adapters accept `live`, `fixture`, and `live_or_fixture`. Live mode fails when data are unavailable; fixture/fallback events have `confidence=scenario`, `metadata.synthetic=true`, no official evidence URL, fixed source dates, and a stated fallback reason when applicable.
- Fed/ECB RSS returns headline triggers with `requires_quantification=true`; a headline cannot satisfy the numeric event schema.
- Inputs reject nonfinite values, inconsistent price changes and nonpositive FX/oil observations. Rate calculations require matching currency scope; foreign-currency mortgages are excluded from home-currency totals.
- Impact output preserves event provenance and source dates alongside formulas. Retail FX fees/spreads are excluded. Fuel and interest pass-through are explicit scenarios, not forecasts.

## Interfaces

Nine MCP tools cover events, headlines, live FX, policy rates, energy, ranking, translation, life-unit conversion and scenario comparison. `docs/TOOL_CONTRACTS.md` lists each tool and REST route. The REST OpenAPI contract is available at `/openapi.json` and is checked in at `verification/openapi.json`.

Tests use deterministic HTTP fixtures and exercise real MCP tool serialization. The separate smoke script proves the actual HTTP mount and protocol negotiation. See `verification/README.md`, `verification/MCP_SMOKE.md`, `docs/DATA_SOURCES.md`, and `docs/JUDGE_DEMO.md`.

## Submission status

The original folder has no Git repository or deployment information. `submission.json` therefore retains explicit deployment/repository placeholders. Set the real repository URL, public deployment origin and exact reviewed commit before submission; no commit or deployment has been invented.
