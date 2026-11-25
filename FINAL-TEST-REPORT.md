# ClaimGuard Phase 06 — Final Build Test Report

Date: 2026-11-12

## Passed

- Active backend regression suite: **67 passed** using `PYTHONPATH=. python -m pytest tests/ -q --disable-warnings --ignore=tests/test_reliability.py`.
- Python compilation: `python -m compileall -q .` passed.
- Changed frontend inline JavaScript: 2 executable script blocks passed `node --check`.
- Static integration checks: expected Phase 01–05 modules are present; legacy patch tree is absent; no active Python import references the archived tree; SearXNG sample URL matches the Docker Compose default port.
- Final archive hygiene: no real `.env` files, local database files, legacy user JSON records, uploads, generated reports, bytecode caches, virtual environments, Git history or nested ZIP archives are packaged.

## Blocked / not run

- Full pytest collection was attempted and stopped at `tests/test_reliability.py` because the build environment does not have `pwdlib` installed (`ModuleNotFoundError`). The project declares `pwdlib[argon2]` in `backend/requirements.txt`; install the complete requirements and run the full suite in the target environment.
- No live PostgreSQL database or migration cutover was available.
- No live SearXNG endpoint or current retailer prices/stock were verified.
- No production browser session, live email delivery, or real-invoice OCR acceptance run was performed.

Passing fixture-based tests and syntax checks do not establish production readiness or guaranteed OCR/market-price accuracy.
