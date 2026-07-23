# Vega: Black Scholes Options Pricer

## Project summary

A full stack Black Scholes options pricer built as a quant interview pet project. The build follows the incremental path described in the source video (REPL, then GUI, then heat map, then P&L heat map, then database persistence) and extends it with the Greeks, real market data, multiple pricing models, and backtesting. The finished product is deployed to the public internet so it can be linked from a resume.

GitHub repository: https://github.com/MustafaNazeer/Vega

## Visual direction

The "Oxblood" theme: dark surface, oxblood `#C03A3A` primary, sea green `#34D399` accent, IBM Plex Serif italic for display, Newsreader for numbers, Manrope for UI text, JetBrains Mono for code. Five screens are in scope: Pricing, Heat Map, Model Comparison, Backtest, and History.

Every design token is defined once as a CSS variable at `:root` in `frontend/src/styles/tokens.css` and documented in `docs/design/tokens.md`, so reskinning the app is a single edit. Screen layout and interaction detail live in `docs/design/wireframes.md`, keyed to the `data-component` and `data-element` names the React components carry. The `design/` folder at the project root holds the original .webp mood board as historical reference.

## Source material

This spec was written from a YouTube transcript describing a recommended pet project for a quant trader candidate, plus the scoping decisions made on top of it. The transcript is kept at `docs/source/transcript.md`. The original video is https://www.youtube.com/watch?v=lY-NP4X455U.

## Tech stack decisions

| Layer | Choice | Rationale |
|---|---|---|
| Frontend framework | React with Vite and TypeScript | Demonstrates real frontend skill, more impressive than a Streamlit prototype on a resume. |
| Frontend styling | Tailwind CSS | Locked in for v1. Tokens come from the Oxblood theme in `docs/design/tokens.md`. May be revisited later. |
| Visual theme | Oxblood (dark) | Primary `#C03A3A`, accent `#34D399`, fonts Manrope (UI), IBM Plex Serif italic (display), Newsreader (numbers), JetBrains Mono (code). |
| Frontend hosting | Cloudflare Pages (primary), Vercel (alternative) | See `docs/setup-guide.md` for the Vercel security tradeoff. |
| Backend framework | FastAPI on Python 3.12 | Async, typed, fast, idiomatic for quant Python. |
| Backend hosting | Render or Fly.io free tier | Vercel does not host long running Python services well. |
| Database | Postgres on Neon free tier (production), SQLite (local dev) | Postgres is free via Neon, SQLite gives zero setup local dev. |
| ORM / migrations | SQLAlchemy 2.x with Alembic | Industry standard for Python plus Postgres. |
| Python package manager | `uv` | Fast, modern, single tool replacing pip plus venv plus pip-tools. |
| Frontend package manager | `pnpm` | Fast and disk efficient. |
| Containerization | Docker plus docker-compose | Local parity with the deployed backend. |
| CI/CD | GitHub Actions | Tests on every PR, deploy on tag. |
| Auth | None for v1 | Per user auth is deferred to a future release. |
| Market data | `yfinance` | Free, no API key required. |
| Charts | Raw Canvas plus SVG, or Recharts/Plotly | Heat maps are drawn on `<canvas>` and line charts inline with SVG, with no third party library. A charting library is allowed later if it materially improves maintainability. |

## Phased build

Each phase is a shippable milestone. Earlier phases must be production ready before the next phase begins. A phase is done when its tests, security review, and code review all pass.

### Phase 0: Foundations

Repository initialization, frontend and backend scaffolds, threat model, wireframes, README, and the architecture doc. The Oxblood tokens are extracted to `docs/design/tokens.md` and a Tailwind config extension, and screen layouts are written to `docs/design/wireframes.md`. The `uv` project lands under `backend/` and the pnpm plus Vite plus React plus TypeScript plus Tailwind project under `frontend/`.

### Phase 1: Python REPL Black Scholes

A pure Python module computes Black Scholes call and put values from the five inputs (current asset price, strike, time to expiry, risk free interest rate, volatility). A small REPL or `__main__` script accepts the five inputs and prints both prices. The formula and its edge cases (T equals zero, sigma equals zero, deep in or out of the money) are covered by tests.

### Phase 2: FastAPI backend

The calculator is wrapped in a FastAPI service. A single endpoint accepts the five inputs and returns call and put values, with Pydantic models validating input. Input validation, CORS policy, rate limiting, and error responses are reviewed. Structured logging and request tracing are added, along with contract tests against the endpoint.

### Phase 3: React frontend MVP

The React plus Vite app ships with a form for the five inputs and a result panel showing call and put, built against the wireframes. An accessibility audit covers keyboard navigation, screen reader labels, and color contrast. CSP, secrets handling, and the frontend to backend calls are reviewed.

### Phase 4: Heat map visualization

A heat map component whose two axes are volatility shock and stock price shock, with cells showing call values and a second heat map showing put values. Cells are colored on a green to red scale by magnitude. Heat map generation is profiled, and the computation is vectorized server side if needed.

### Phase 5: P&L heat map

Two new fields expose the purchase price for the call and for the put. The heat map switches mode to display P&L instead of value, green for positive and red for negative. P&L sign and magnitude are validated under stress cases.

### Phase 6: Persistence

Schema design (inputs table, outputs table, `calculation_id` linking them), Alembic migrations and indexes, and the persistence layer, so every Calculate click writes one row to the inputs table and N rows to the outputs table, one per heat map cell. SQL injection surface, parameterized queries, secrets in DSNs, and least privilege for the DB user are all reviewed.

### Phase 7: The Greeks

Closed form Greeks (delta, gamma, theta, vega, rho) are added to the Black Scholes module, exposed by the backend, and displayed next to the call and put values. Property based tests cover put call parity and delta bounds.

### Phase 8: Real market data

A service calls `yfinance` to look up the current price for a ticker symbol, and the frontend gains ticker autocomplete that auto fills the asset price field on selection. The third party request path is reviewed for timeouts, response size limits, and retry policy.

### Phase 9: Multiple pricing models

A binomial tree model and a simple Monte Carlo pricer join Black Scholes, with a model selector in the UI and a side by side comparison of the three prices. The three models are checked for convergence on identical inputs.

### Phase 10: Backtesting

A backtesting endpoint takes a strategy (long call, covered call, straddle) and a date range, replays historical prices, and produces a P&L curve that the frontend renders. Time and memory cost are reviewed.

### Phase 11: Production deployment

Frontend to Cloudflare Pages, backend to Render, database on Neon, plus a custom subdomain if wanted. A final hardening pass covers HTTPS, HSTS, CSP, secret rotation, and dependency scanning, and the user facing setup guide at `docs/setup-guide.md` is finalized and verified.

## Quality rules

1. No phase begins until the prior phase has passing tests, a security review, and a code review.
2. Every code change is reviewed before it merges.
3. Anything touching secrets, third party calls, or user input gets a security review against `docs/security/checklist.md`.
4. Anything touching the pricing math or the P&L calculation gets a correctness review against the sign and units conventions in `docs/risk/conventions.md`.
5. `docs/` is updated whenever a phase completes.
6. Ideas out of scope for v1 are recorded outside this repository and not implemented in v1.
