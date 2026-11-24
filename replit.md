# NEXUS: Intelligent Multi-Agent Decision Support System

NEXUS analyzes uploaded documents and CSV data to produce transparent, evidence-linked decision reports. Its modules are deterministic; it does not perform web research or claim to train a model.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the FastAPI backend (API path `/api`)
- `pnpm --filter @workspace/nexus-decision-support run dev` — run the React + Vite frontend
- `uv run pytest artifacts/api-server/tests -q` — run FastAPI tests
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm --filter @workspace/api-server run build` — verify the FastAPI package
- `PORT=23480 BASE_PATH=/ pnpm --filter @workspace/nexus-decision-support run build` — build the web artifact
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- Default database: SQLite at the repository root (`nexus.db`)
- Optional database setting: `NEXUS_DATABASE_URL` (SQLAlchemy URL; PostgreSQL URLs use `postgresql+psycopg://`)

## Stack

- pnpm workspace, React, TypeScript, Vite
- Backend: Python 3.13+, FastAPI, SQLAlchemy
- Database: SQLite by default; PostgreSQL through `NEXUS_DATABASE_URL`
- Analytics and retrieval: Pandas, NumPy, scikit-learn TF-IDF
- Document extraction: pypdf and python-docx
- Report exports: Markdown and PDF (ReportLab)
- API hooks and validation: Orval-generated from `lib/api-spec/openapi.yaml`

## Where things live

- `artifacts/nexus-decision-support` — React dashboard, analysis form, and detail views
- `artifacts/api-server/nexus_api` — FastAPI routes, database setup, deterministic modules, and report generation
- `artifacts/api-server/tests` — API and analysis tests
- `lib/api-spec/openapi.yaml` — API contract used for generated client hooks
- `README.md` and `.env.example` — local setup, configuration, and deployment instructions

## Architecture decisions

- Keep FastAPI as the backend; do not replace it with the workspace's original Express scaffold.
- Use SQLite by default for local setup; opt into PostgreSQL with `NEXUS_DATABASE_URL`.
- Process uploads as analysis inputs and persist results, not original file bytes.
- Label the demo as synthetic and run it through the same real deterministic pipeline.

## Product

Users can create decision analyses from documents and CSV files, inspect evidence and data-quality findings, review bounded critic passes, browse persisted history, and download Markdown/PDF reports.

## User preferences

- Keep the backend as FastAPI; do not silently substitute another backend.
- Do not claim fake evidence, analysis execution, external research, or model training.

## Gotchas

- Regenerate the OpenAPI client after editing `lib/api-spec/openapi.yaml`.
- For local browser development, set `VITE_API_BASE_URL=http://localhost:8080` when the API runs on port 8080.

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
