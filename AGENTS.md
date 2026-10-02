# Base44 Dev Environment

## What this app is
A Flask-based Brazilian algorithmic trading dashboard (`dashboard/app.py`) with:
- Flask-Login authentication (all app routes are `@login_required`; `/auth/login`, `/auth/register`, `/api/tickers` are public).
- SQLite storage at repo-root `trades.db` (auto-created on boot via `init_db()` / `init_auth_db()`; gitignored).
- Market data via `yfinance` (no key needed). Dividend data via Brapi.dev (a fallback token is hardcoded in `data/dividendos_collector.py`; `BRAPI_API_KEY` overrides it).
- Optional AI advisor features via OpenAI (degrade gracefully when `OPENAI_API_KEY` is absent).

## Running it
`docker compose -f docker-compose.base44.yml up -d` — single `web` service on host port 3000.
- Base image `python:3.11-slim`; repo bind-mounted at `/app`; deps installed from `requirements.txt` on each start.
- Runs `flask --app dashboard.app:app run --debug --host 0.0.0.0 --port 3000 --reload` (Werkzeug reloader → edits hot-reload).
- Healthcheck probes `GET /auth/login`.

## First use
There is no default user. Register one via the UI at `/auth/register` (email + password ≥6 chars), which logs you in and redirects to the dashboard.

## Secrets
None required to boot. Optional external credentials (`OPENAI_API_KEY`, `BRAPI_API_KEY`, `SECRET_KEY`) are declared in `.base44/environment.json`. A dev `SECRET_KEY` placeholder lives in `.env.base44-defaults` so sessions survive reloader restarts; real values delivered via `/run/base44/app.env` override it.

## Notes
- `main.py` is the headless CLI pipeline (not the web entry point); the web app is `dashboard/app.py`.
- `dashboard/app_simples.py` is an alternate/simpler dashboard not wired into compose.
