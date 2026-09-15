# Verification runbook

```powershell
uv sync --locked --extra dev
uv run --frozen python -m pytest
uv run --frozen python verification/smoke_mcp.py
uv run --frozen python verification/smoke_mcp.py --live-fx
uv run --frozen python verification/generate_fixtures.py
```

The smoke script owns a temporary loopback Uvicorn process and stops it on success/failure. Default mode proves the synthetic MCP flow without external requests. `--live-fx` additionally exercises actual official RSS and FX; a live failure is recorded as failure, never as a successful fallback. `MCP_SMOKE.md` describes the latest retained run; machine evidence is in `mcp-smoke.json` and `end-to-end-demo.json`.

`generate_fixtures.py` regenerates OpenAPI, the fixed FX request/response, invalid input and policy/energy fixture requests/responses, and the offline ranked demo. `tests/test_verification.py` checks the saved contract and fixed core response. Provider fixture dates are synthetic; captured live source dates and retrieval times remain distinct.

Run the app with `uv run --frozen uvicorn life_exchange_rate.main:app --app-dir source --port 8000`. Check `/health`, `/.well-known/xagent-verification.json`, `/openapi.json`, `/v1/events/demo`, `/v1/radar/demo`, and `/mcp/` using a real MCP client. A raw browser GET is not a tool call. POST `fixtures/fx-request.json` to `/v1/translate` and compare the response to `fixtures/fx-response.json`.

## Deployment handoff

The delivered source directory was not a Git repository and no public deployment was supplied. Do not submit `dev-local` or invented deployment URLs. On the deployed reviewed version set `REVIEW_COMMIT` to its exact Git SHA and `PUBLIC_BASE_URL` to the public origin, then rerun health/proof and MCP checks on that deployment. Both endpoints share package version and commit. Fill `submission.json` and `SUBMISSION.md` with those verified values. The MCP host allowlist derives from the configured public origin while retaining loopback for local use.

EIA live validation requires `EIA_API_KEY`. Automated EIA success/error cases use mocked official-shaped responses; missing-key fallback is tested explicitly. No live EIA result should be claimed unless a keyed request was actually run.


## Hosted review calls (populate after real deployment)

Set API_ORIGIN to the verified production HTTPS origin, then run from the project root:

```bash
curl --fail --silent --show-error "$API_ORIGIN/health"
curl --fail --silent --show-error "$API_ORIGIN/.well-known/xagent-verification.json"
curl --fail --silent --show-error "$API_ORIGIN/v1/translate" --header 'Content-Type: application/json' --data-binary @verification/fixtures/fx-request.json
curl --silent --show-error --write-out '\nHTTP %{http_code}\n' "$API_ORIGIN/v1/translate" --header 'Content-Type: application/json' --data-binary @verification/fixtures/invalid-request.json
```

The capability fixture expects 1,739.13 SEK / 8.70 work hours. The invalid request expects HTTP 422. Hosted evidence must be captured after deployment; localhost records alone are insufficient. Use curl.exe on Windows PowerShell when curl is aliased.
