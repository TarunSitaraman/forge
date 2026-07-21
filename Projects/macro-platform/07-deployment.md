# Deployment

## Local Setup (Verified from README)

```bash
# 1. Virtual environment
python -m venv .venv
.\.venv\Scripts\activate     # Windows
# source .venv/bin/activate  # Mac/Linux

# 2. Dependencies
pip install -r requirements.txt
playwright install           # Browser binaries for the web crawler agent

# 3. Environment config — copy .env.example to .env and fill in:
#    APP_ENV, DATABASE_URL, JINA_API_KEY, GEMINI_API_KEY,
#    GROQ_API_KEY, API_SECRET_KEY

# 4. Database setup
python db_init.py            # Create tables, seed global indicators
python register_admin.py     # Create default admin user

# 5. Run services (three separate terminals)
uvicorn src.api.main:app --reload           # FastAPI — localhost:8000
dagster dev -f src/orchestration/jobs.py    # Dagster — localhost:3000
streamlit run src/ui/app.py                 # Streamlit — localhost:8501
```

**Default admin credentials (local dev seed only):**
`admin@example.com` / `admin123` — per `register_admin.py`. Treat as a
placeholder to rotate immediately in any non-local environment, not a
real credential to rely on past initial setup.

## Environment Variables (from `.env.example` reference in README)

```env
APP_ENV=development
DATABASE_URL=postgresql://user:pass@localhost:5432/macro_db
JINA_API_KEY=your_key
GEMINI_API_KEY=your_key
GROQ_API_KEY=your_key
API_SECRET_KEY=your_secure_jwt_secret
```

FRED and OpenRouter keys are also required per the requirements section
of the README, though not shown in this exact `.env` snippet — confirm
full variable list directly against `.env.example` before assuming this
list is exhaustive.

## Docker

`docker/Dockerfile.api` and `docker/Dockerfile.streamlit` — separate
images for the FastAPI backend and Streamlit frontend, plus
`docker/docker-compose.yml` for orchestrating both together (likely
alongside a Postgres service, though not confirmed directly). This
suggests a three-service deployment topology (API, UI, DB) as the
container-based alternative to the three-terminal local setup above.

## Hosting

`render.yaml` is present at the repo root, indicating a Render-based
deployment path (consistent with [Personal Agent](../personal-agent/)'s
earlier Render usage and [QuickCover](../quickcover/)'s current Render
backend — Render appears to be Tarun's default choice for Python/Node
backend hosting across projects).

## Common Mistakes to Avoid

- **Skipping `playwright install`** — the web crawler agent
  (`src/agents/crawler.py`) depends on Playwright's browser binaries;
  without this step, Bronze-layer crawler ingestion will fail silently
  or with an unclear browser-not-found error.
- **Running services out of order** — Dagster jobs and the Streamlit UI
  both expect the database to already be initialized (`db_init.py`) and
  the API's auth to have a seeded admin (`register_admin.py`); starting
  these before database setup will surface as confusing downstream
  errors rather than a clear "run db_init.py first" message.
- **Leaving default admin credentials unrotated** past local
  development — this is a real credential once deployed anywhere
  reachable.

## Related Docs

[Architecture](./02-architecture.md) for the tech stack this deployment
setup provisions.
