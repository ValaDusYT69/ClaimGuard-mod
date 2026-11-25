# ClaimGuard — Phase 04: Price Comparison & Risk Scoring

## Apply order

Apply **Phase 01 → Phase 02 → Phase 03 → Phase 04** in that order. This package is built on the same fixed-v3 project baseline used by the earlier replacement packages, with the Phase 01–03 files layered on top.

## Files to replace / add

| Action | Project path | Package path |
|---|---|---|
| Add | `backend/invoice/price_comparison.py` | `backend/invoice/price_comparison.py` |
| Replace | `backend/invoice/risk_engine.py` | `backend/invoice/risk_engine.py` |
| Replace | `backend/invoice/routes.py` | `backend/invoice/routes.py` |
| Replace | `backend/invoice/report_service.py` | `backend/invoice/report_service.py` |
| Replace | `frontend/invoice-details.html` | `frontend/invoice-details.html` |
| Add | `backend/tests/test_price_comparison.py` | `backend/tests/test_price_comparison.py` |

Copy the package folders into the project root and allow the listed files to be replaced. Do not replace other files.

## What this phase adds

- Recalculates each product-line benchmark from accepted page-level market observations.
- Requires at least two normalized retailer domains for an average, lowest/highest/median, percentage difference, price alignment, and price risk points.
- Excludes observations rejected by the existing price-observation classifier, including search-snippet-only, out-of-stock, invalid-price and invalid-currency results. Explicitly unverified or mismatched variants are also excluded.
- Keeps currencies separate and only compares against the invoice currency when it is known.
- Counts one observation per normalized retailer domain; common subdomains are collapsed to avoid duplicate weighting.
- Keeps each invoice line independent. Repeated product names remain separate lines and different SKUs are never combined into an invoice-wide price average.
- Adds per-line price comparison status, independent-domain count, difference direction, alignment, match confidence, and a rule-based price-risk contribution.
- Uses a spend-weighted average of comparable product-line risk contributions (maximum 30 points for the market-price component). The market-price contribution is added once to deterministic risk scoring.
- Preserves the previous single-product risk interface for older saved invoices without the new comparison structure.
- Explicitly skips the existing invoice-level Isolation Forest for multi-SKU invoices because its current feature vector expects one product line; this avoids treating one product's unit price as representative of a whole multi-product invoice. Deterministic product-line market scoring remains active.
- Shows the per-line comparison and price-risk contribution in invoice details and in the generated PDF.

## Scoring definitions

- **Market match confidence**: average identity-confidence values from accepted sources. It is not OCR accuracy or fraud probability.
- **Price alignment**: `max(0, 100 - absolute percentage difference)` from the verified market average. It is not fraud probability.
- **Price risk points**: 0 points at or below 15% absolute difference, rising linearly to 30 points at 50% difference or more. It flags unusually high **or** unusually low invoice prices for human review; it does not prove fraud.
- **Invoice risk score**: includes deterministic rules and, only when enough genuine historical records exist, the previously configured local ML contribution. A market-price comparison alone contributes at most 30 points.

The source-independence rule is domain-based. Separate domains are not proof that the retailers have separate corporate ownership.

## Targeted verification

From `backend/`, run:

```bash
python -m py_compile invoice/routes.py invoice/risk_engine.py invoice/price_comparison.py invoice/report_service.py
python -m pytest -q tests/test_core_runtime.py tests/test_extraction_quality.py tests/test_price_comparison.py tests/test_free_market.py tests/test_market_variant_accuracy.py tests/test_invoice_intelligence.py tests/test_report_service.py
```

The Phase 01–04 combined targeted suite passed **53 tests** in the package-build environment. The invoice-details inline JavaScript also passed `node --check`.

A full test-suite collection was attempted, but it stops before running all tests because the build environment does not have the existing backend dependency `pwdlib` installed (`ModuleNotFoundError`). Install the project's backend requirements in the target environment and rerun the full suite.

## Apply safely

1. Back up the entire project and database.
2. Stop the backend before copying replacement files.
3. Apply the phases in order.
4. Keep your existing `.env`, PostgreSQL database, uploads, OCR assets, invoices and generated reports. This ZIP contains none of those.
5. Restart the backend and re-run targeted tests. Then re-run market analysis on a test invoice and inspect the saved product lines and PDF.

## Verification limits

Tests are deterministic and use controlled source fixtures. Live retailer availability, SearXNG connectivity, live website prices, production PostgreSQL behavior and real-invoice risk calibration were not verified during this package build. Price-risk points are a transparent heuristic, not a statistically validated fraud model.
