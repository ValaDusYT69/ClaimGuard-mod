# ClaimGuard — Phase 05: PDF Report & Dashboard Integration

This is a standalone replacement package for Phase 05. Apply Phases 01, 02, 03 and 04 first.

## Files in this package

| Package path | Action in your project |
|---|---|
| `backend/invoice/report_service.py` | Replace the existing file |
| `frontend/user-dashboard.html` | Replace the existing file |
| `backend/tests/test_phase5_report_dashboard.py` | Add this test file |

## What changes

### PDF report

- Keeps the Phase 04 per-product market comparison and evidence/source-link sections.
- Adds the OCR extraction completeness heuristic and explicit extraction warnings.
- Shows fields not detected by the extractor separately from fields that may simply not appear on the original invoice.
- Includes structured invoice totals and payment/order details when those fields exist.
- Shows field provenance recorded by the extraction pipeline. Any displayed confidence is explicitly labelled heuristic, not OCR accuracy.
- Adds product-line detail for quantity, SKU/model, unit price, discount, tax, line total, row warnings and other structured line fields.
- Continues to use persisted data only; it does not invent a market average, source or risk score.
- Does not print the raw OCR transcript into the report.

### User dashboard

- Adds per-invoice market coverage (number of comparable product lines / total product lines).
- Adds a PDF button next to View for every invoice in the user's list.
- Uses the existing authenticated `GET /api/invoices/{invoice_id}/report` endpoint; no new database table, API route or storage format is introduced.
- Keeps the invoice-details page's existing PDF download action intact.

## Apply steps

1. Stop the backend and make a backup of the project folder.
2. Keep your existing `.env`, PostgreSQL database, uploads, OCR assets, invoice records and generated reports. Do not replace these with this package.
3. Apply the three file actions in the table above.
4. From the backend folder, run:

   ```bash
   python -m compileall -q .
   python -m pytest tests/test_phase5_report_dashboard.py tests/test_report_service.py -q
   ```

5. Start the backend and frontend. Open the user dashboard, select an invoice, and use its **PDF** action. The user must be logged in and must own the invoice (Admin/Finance access continues to follow the existing backend authorization rules).

## Dependencies and verification boundary

The implementation uses the existing ReportLab PDF service and existing invoice report endpoint. It does not add a new runtime dependency.

Authoring checks: 11 focused PDF/report/dashboard tests passed; JavaScript syntax check passed; the active backend regression suite passed 62 tests when excluding the known unavailable-dependency reliability test and the bundled legacy `cg_patch` tests. A completely unfiltered suite could not be reported as clean: `pwdlib` is absent in this build environment, and two legacy `cg_patch` tests target an older market-research interface. Live PostgreSQL, browser download, and PDFs generated from your own invoices still need verification in your environment.

## Replacement safety

This phase replaces only the PDF report service and user dashboard page, and adds one test. It does not include or overwrite `.env`, databases, uploads, OCR model files, user data or generated reports.
