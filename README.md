# NEXUS Decision Support

NEXUS is a full-stack, evidence-focused decision support app. It processes PDF, DOCX, TXT, Markdown, and CSV uploads; retrieves relevant document passages with TF-IDF; computes CSV diagnostics; runs deterministic document, data, risk, critic, and synthesis modules; stores completed analyses; and exports Markdown or PDF reports.

The application does not call an LLM or web search provider. It does not train a model. Recommendations are rule-based and require human review. The built-in demo uses clearly labeled fictional inputs and runs through the same pipeline as a user analysis.

## Requirements

- Node.js 24 and pnpm
- Python 3.13+
- `uv`

## Local setup

From the repository root:

```bash
pnpm install
uv sync --all-groups
```

Set the default local database explicitly if desired:

```bash
export NEXUS_DATABASE_URL="sqlite:///./nexus.db"
```

Start the API and frontend in separate terminals:

```bash
PORT=8080 pnpm --filter @workspace/api-server run dev
```

```bash
PORT=5173 BASE_PATH=/ VITE_API_BASE_URL=http://localhost:8080 \
  pnpm --filter @workspace/nexus-decision-support run dev
```

Open the frontend at `http://localhost:5173`. FastAPI interactive documentation is at `http://localhost:8080/api/docs`; health is at `http://localhost:8080/api/healthz`.

The frontend normally uses relative `/api` requests through Replit's shared router. `VITE_API_BASE_URL` is only needed when running the frontend and API on separate local ports.

## Database configuration

When `NEXUS_DATABASE_URL` is unset, NEXUS creates a SQLite database at the repository root (`nexus.db`). For PostgreSQL, set `NEXUS_DATABASE_URL` to a SQLAlchemy URL such as:

```text
postgresql+psycopg://USER:PASSWORD@HOST:5432/DATABASE
```

Keep credentials in your local environment or Replit Secrets; do not put them in `.env.example`, source control, or this README. Tables are created on application startup. Back up `nexus.db` before deleting or replacing it.

## Tests and build

```bash
uv run pytest artifacts/api-server/tests -q
pnpm run typecheck
pnpm --filter @workspace/api-server run build
PORT=23480 BASE_PATH=/ pnpm --filter @workspace/nexus-decision-support run build
```

The API test suite covers health, docs, local CORS, PDF/DOCX/Markdown extraction, a document-plus-CSV workflow, persistence and dashboard metrics, synthetic demo labeling, report downloads, and invalid uploads.

## API

- `GET /api/healthz`
- `GET /api/dashboard`
- `GET /api/analyses`
- `POST /api/analyses` (multipart form)
- `POST /api/analyses/demo`
- `GET /api/analyses/{id}`
- `GET /api/analyses/{id}/report/markdown`
- `GET /api/analyses/{id}/report/pdf`
- `GET /api/docs` (Swagger UI)

Uploads are processed in memory and their original bytes are not persisted. File size, document count, extracted-text size, and CSV row count are bounded.

## Publish / deployment

1. Run the tests and build commands above.
2. In Replit, publish the project so both the React web app and FastAPI API service are included.
3. For persistent deployment data, configure `NEXUS_DATABASE_URL` in the deployment environment to a PostgreSQL connection URL. SQLite is suitable for local development but may not be durable across autoscaled deployment instances.
4. Check `/api/healthz` and `/api/docs` on the published app, then run a clearly synthetic demo before a real analysis.

No API key is required for the implemented deterministic workflow.

## Export source code

In Replit, open the file tree's three-dot menu and select **Download as zip**. If the project is too large for that download, create a source archive from the Shell and then download it from the file tree:

```bash
zip -r nexus-source.zip . \
  -x 'node_modules/*' '*/node_modules/*' \
     '.pythonlibs/*' '*/.pythonlibs/*' \
     '.venv/*' '*/.venv/*' \
     '*/__pycache__/*' '*/.pytest_cache/*' \
     'nexus.db' 'nexus-source.zip'
```

The archive keeps the source, dependency lockfiles, API contract, and setup instructions while excluding installed packages, test caches, and the local database.

## Current limitations

- TF-IDF ranks lexical similarity; it does not verify factual accuracy or source reliability.
- Numeric correlations are Pearson correlations between numeric CSV columns only and do not imply causation.
- Scanned image-only PDFs may not yield extractable text.
- The system does not perform external research, train or evaluate an ML model, or make a final decision on the user's behalf.
- There is no authentication or multi-user ownership model in this minor-project build.
