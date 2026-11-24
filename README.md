# NEXUS — Decision Support System

NEXUS is a full-stack, evidence-focused decision-support application that turns uploaded documents and CSV datasets into structured analysis reports.

**Live demo:** https://nexus-decision-support-system--ashaunticx.replit.app/

## Features

- Upload PDF, DOCX, TXT, Markdown, and CSV files.
- Extract document text and retrieve relevant passages using TF-IDF.
- Compute descriptive diagnostics for CSV datasets.
- Run deterministic document, data, risk, critic, and synthesis modules.
- Store completed analyses and display dashboard metrics.
- Export reports as Markdown or PDF.
- Explore a clearly labelled fictional demo workflow.

## How it works

The application combines a React frontend with a FastAPI backend. Its analysis pipeline uses document processing, lexical retrieval, dataset diagnostics, rule-based risk checks, and report synthesis.

> **Transparency:** The current implementation does not call an LLM or external web-search provider, and it does not train a machine-learning model. Recommendations are rule-based decision support and require human review. TF-IDF measures lexical similarity; it does not establish factual accuracy or source reliability.

## Tech stack

- **Frontend:** React, TypeScript, Vite
- **Backend:** Python, FastAPI
- **Data processing:** Pandas, NumPy
- **Retrieval:** TF-IDF
- **Database:** SQLite by default; PostgreSQL supported through configuration
- **API documentation:** OpenAPI / Swagger UI

## Project structure

- `artifacts/nexus-decision-support/` — main web application
- `artifacts/api-server/nexus_api/` — FastAPI backend and analysis pipeline
- `lib/api-spec/` — API specification
- `lib/api-client-react/` and `lib/api-zod/` — generated API client and schemas

## Run locally

### Requirements

- Node.js 24+
- pnpm
- Python 3.13+
- `uv`

### Install dependencies

```bash
pnpm install
uv sync --all-groups
```

### Configure the database

By default, NEXUS creates a local SQLite database named `nexus.db`. You can set the URL explicitly:

```bash
export NEXUS_DATABASE_URL="sqlite:///./nexus.db"
```

For PostgreSQL, set a SQLAlchemy URL in your local environment. Do not commit database credentials.

### Start the backend

```bash
PORT=8080 pnpm --filter @workspace/api-server run dev
```

### Start the frontend

In a second terminal:

```bash
PORT=5173 BASE_PATH=/ VITE_API_BASE_URL=http://localhost:8080 pnpm --filter @workspace/nexus-decision-support run dev
```

Open `http://localhost:5173`.

- API health: `http://localhost:8080/api/healthz`
- Swagger docs: `http://localhost:8080/api/docs`

## API endpoints

- `GET /api/healthz`
- `GET /api/dashboard`
- `GET /api/analyses`
- `POST /api/analyses`
- `POST /api/analyses/demo`
- `GET /api/analyses/{id}`
- `GET /api/analyses/{id}/report/markdown`
- `GET /api/analyses/{id}/report/pdf`
- `GET /api/docs`

## Tests and build

```bash
uv run pytest artifacts/api-server/tests -q
pnpm run typecheck
pnpm --filter @workspace/api-server run build
PORT=23480 BASE_PATH=/ pnpm --filter @workspace/nexus-decision-support run build
```

## Known limitations

- TF-IDF ranks lexical similarity; it does not validate facts.
- Numeric correlations are Pearson correlations and do not imply causation.
- Scanned image-only PDFs may not yield extractable text.
- The current project has no authentication or multi-user ownership model.
- The system does not make final decisions on a user's behalf.

## License

No license has been added yet. All rights remain with the respective author(s) until a license is selected.
