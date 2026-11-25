# ClaimGuard Phase 01 — Core Backend Replacement

This phase replaces the core runtime/configuration/database-adapter files while preserving the current API route contracts and existing user/invoice storage schema.

## Replace these files

- `backend/config.py`
- `backend/main.py`
- `backend/data/connection.py`
- `backend/data/database.py`
- `backend/.env.example` (template only; do NOT overwrite your real `.env`)
- `backend/requirements.txt`

## Add these files

- `backend/requirements-dev.txt`
- Root `README.md`
- Root `.gitignore`

## Remove these obsolete files/folders from the source tree

- `backend/package-lock.json` — there is no Node `package.json` or Node-based backend build.
- `backend/cg_patch/` — archived patch copies are not runtime code and must not be imported.

The clean release archive intentionally omits `.git`, `.venv`, `__pycache__`, `.pytest_cache`, real `.env` files, database files, JSON user/OTP/invoice stores, customer uploads, and generated reports. Do not remove your live data while applying this phase.

## Important runtime behavior changes

- PostgreSQL is required in normal runtime. The app will fail with an explicit configuration message instead of silently using SQLite.
- SQLite remains available only to isolated tests/migration tooling that explicitly overrides the database path.
- Database schema initialization is cached per database per process, so auth/storage calls no longer repeat all `CREATE TABLE` and `CREATE INDEX` statements on every request.
- CORS uses exact origins from `ALLOWED_ORIGINS`; credentialed wildcard CORS is removed.
- Startup validates required runtime configuration and initializes the current schema.
- `/api/health/live` and `/api/health/ready` are added. Existing `/api/health` remains available and reports DB failures as HTTP 503.

## Apply safely

1. Back up the existing project and database.
2. Stop the backend.
3. Replace/add the listed code files only.
4. Keep your real `backend/.env`, `searxng/.env`, database, `backend/uploads/`, and `backend/reports/` untouched.
5. Install dependencies using `python -m pip install -r backend/requirements.txt`.
6. Ensure `DATABASE_URL` and `ALLOWED_ORIGINS` in your real `.env` are correct.
7. Start the API from `backend/` using `python -m uvicorn main:app --reload --host 127.0.0.1 --port 8000`.

This is Phase 01 only. OCR/product extraction, market engine, scoring, PDF/dashboard, and full durable automation are handled in the subsequent replacement phases.

## Phase 01 verification

- `python -m compileall -q .` — passed.
- `python -m pytest tests -q --ignore=tests/test_reliability.py` — **44 passed**.
- `tests/test_reliability.py` could not be collected in this build container because the `pwdlib` package is not installed in the container interpreter. `pwdlib[argon2]` remains declared in `requirements.txt`; install the requirements and rerun the full suite locally.
- No live PostgreSQL server was available here, so database connectivity was not claimed as verified.
