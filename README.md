# Vega

**Live demo: <https://vega-2rd.pages.dev/>**

The backend runs on Render's free tier and sleeps after about 15 minutes of inactivity, so the first calculation after a long idle period can take 30 to 60 seconds while the container wakes up. After that, requests are instant.

A full stack Black Scholes options pricer built as a quant interview pet project. The app prices European calls and puts from five inputs (asset price, strike, time to expiry, risk free rate, volatility), renders heat maps of value and P&L over volatility and price shocks, exposes the Greeks, looks up live prices via yfinance, compares Black Scholes against a binomial tree and a Monte Carlo pricer, and runs simple option strategy backtests over historical data.

## Visual theme

**Oxblood**: a dark surfaced theme with oxblood `#C03A3A` as primary, sea green `#34D399` as accent, IBM Plex Serif italic for display, Newsreader for numerics, Manrope for UI text, and JetBrains Mono for code. Every token is defined once as a CSS variable in [`frontend/src/styles/tokens.css`](frontend/src/styles/tokens.css) and documented in [`docs/design/tokens.md`](docs/design/tokens.md), so reskinning the app is a single `:root` edit.

## Tech stack at a glance

| Layer | Choice |
|---|---|
| Frontend | React 18 plus Vite plus TypeScript plus Tailwind, hosted on Cloudflare Pages |
| Backend | FastAPI on Python 3.12, hosted on Render |
| Database | Postgres on Neon (production), SQLite (local dev) |
| Migrations | SQLAlchemy 2.x plus Alembic |
| Package managers | `uv` (Python), `pnpm` (JS) |
| CI/CD | GitHub Actions |
| Market data | `yfinance` |

## Entry points

* [`SPEC.md`](SPEC.md): the full project spec and the 11 stage build plan.
* [`docs/architecture.md`](docs/architecture.md): high level system diagram and component descriptions.
* [`docs/setup-guide.md`](docs/setup-guide.md): user facing deployment guide (Cloudflare, Render, Neon, custom domain).
* [`docs/devops.md`](docs/devops.md): local development walkthrough and the deploy runbook.
* [`docs/design/tokens.md`](docs/design/tokens.md) and [`docs/design/wireframes.md`](docs/design/wireframes.md): the Oxblood tokens and the per screen layout and interaction model.
* [`docs/adr/`](docs/adr/): Architecture Decision Records for the non obvious calls (React over Streamlit, Postgres over MySQL, Cloudflare Pages over Vercel, Oxblood as v1 visual).

## How to run locally

The full local development walkthrough lives at [`docs/devops.md`](docs/devops.md). It covers `uv sync` for the backend, `pnpm install` for the frontend, and the `docker compose up` flow once it lands.

For the abridged version, see "Local development setup" in [`docs/setup-guide.md`](docs/setup-guide.md).

## Status

v1 is unauthenticated by design (every visitor sees the same calculation history). Per user history is deferred to a future release.
