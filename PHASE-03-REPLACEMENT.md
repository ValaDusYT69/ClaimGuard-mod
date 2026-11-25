# ClaimGuard Phase 03 — Automatic Market Intelligence

## Apply order
Apply Phase 01 and Phase 02 first. This package is built against the ClaimGuard fixed-v3 baseline used for those replacement packages.

## Replace/add these files
- Replace `backend/invoice/market_research.py` with the file in this package.
- Replace `backend/tests/test_free_market.py` with the test file in this package.

No `.env`, database, uploads, reports, OCR model files, or user data are included. Back up the project and database before replacing files. Do not overwrite your `.env` with `.env.example`.

## What this phase changes
- Runs market analysis separately for each extracted invoice line.
- Accepts common line-item product-name keys (`product`, `product_name`, `description`, `name`, `item_description`) and unit-price keys (`unit_price`, `price`).
- Preserves separate invoice lines, even when two lines have the same product name.
- Keeps retailer/source attribution tied to the relevant invoice line. A single retailer domain can appear for multiple different products without one product overwriting another.
- Adds `line_index`, quantity, invoice unit price, line total, source count, and independent-domain count to each product analysis.
- Does not calculate an invoice-wide average across different SKUs. Per-product statistics remain inside each product's `market` result.
- Keeps the existing variant checks, page-level price evidence requirements, source diagnostics, and configured SearXNG integration.

## Verification
From `backend/` run:

```bash
python -m py_compile invoice/market_research.py
python -m pytest -q tests/test_free_market.py tests/test_market_variant_accuracy.py tests/test_invoice_intelligence.py
```

The package was checked with Python compile and these targeted tests: **30 passed**.

A full test-suite collection was attempted but is blocked in this environment because `pwdlib` is not installed (`ModuleNotFoundError`). Install the backend dependencies in your project environment and rerun the complete suite.

## Important limits
This phase improves orchestration and attribution; it does not guarantee that every retailer is reachable or that every product will have a comparable live listing. A price is only as reliable as the existing page extraction and variant validation. Search snippets are not treated as page-verified prices. Missing/ambiguous evidence must remain missing/unverified, not be fabricated. Live retailer/SearXNG integration was not tested against your running environment in this package build.
