# ClaimGuard — Phase 06: Final Integration & Cleanup

## Consolidated state

This folder contains the merged Phase 01–05 application sources, not merely the Phase 06 patch. The Phase 06 work removes verified archived patch copies and generated Python test/runtime caches, refreshes the top-level documentation, and adds an integrity test.

## Cleanup performed in this clean package

- Removed `backend/cg_patch/`. A source scan found no Python runtime import/reference to this archived directory. Its old tests were being discovered by pytest and conflicted with the active market research implementation.
- Removed generated `__pycache__/`, `.pytest_cache/`, bytecode and coverage artifacts.
- Kept the active modules, migrations, frontend pages, setup documentation and tests.
- Aligned the backend `SEARXNG_URL` sample to `http://127.0.0.1:8080`, matching the included Docker Compose default port; existing `backend/.env` is intentionally untouched.
- Did not include `.env`, database files, user/OTP/invoice JSON, uploads, reports, OCR model data, `.venv`, Git history or nested archives.
- `backend/package-lock.json` and `ClaimGuard_AJ199_CarbonFiber_PDF_Report_V4.zip` were not present in this consolidated snapshot; if they exist in an older local folder, move them outside the project after backup rather than assuming they are source files.

## If applying Phase 06 to an existing Phase 01–05 project

1. Stop the backend and back up the entire project and database.
2. Copy the files from the Phase 06 replacement ZIP to the project root and allow the listed documentation/test files to be added or replaced.
3. Remove `backend/cg_patch/` only after preserving a backup if you need the archived patch history.
4. Remove generated Python caches (`__pycache__`, `.pytest_cache`, `.pyc`) if present.
5. If `backend/package-lock.json` exists without a corresponding `backend/package.json`, move it to the backup/archive location. Do not delete a real lockfile for a package manifest.
6. Never delete or replace `.env`, `searxng/.env`, databases, legacy JSON data, uploads, reports, OCR assets or user files.
7. Run the tests below and smoke-test the app against a copy of the existing database and sample invoice before cutover.

## Verification performed for this package

- Python `compileall`: passed.
- Active backend tests excluding `test_reliability.py`: 67 passed in the final Phase 06 run.
- Changed invoice-details and user-dashboard inline JavaScript: 2 executable script blocks passed `node --check`.
- `backend/cg_patch/` removed; no active Python source import reference found.
- SearXNG sample endpoint and Docker Compose default port verified as `8080`.
- The unfiltered test collection in this build environment is blocked because `pwdlib` is not installed (`ModuleNotFoundError`). The dependency is declared in `backend/requirements.txt`; install the full requirements and rerun the full suite in the target environment.

## Not verified

No live PostgreSQL instance, SearXNG endpoint, retailer search, real-time retailer stock/price, production browser session, or real-invoice OCR acceptance run was available during this package build. These must be checked in the target environment. Risk points and field confidence remain heuristics, not validated fraud probabilities or OCR accuracy metrics.

## Run checks

From `backend/`:

```bash
PYTHONPATH=. python -m pytest tests/ -q
python -m compileall -q .
```

On Windows PowerShell, set `$env:PYTHONPATH = "."` before running pytest.
