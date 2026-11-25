# ClaimGuard Phase 02 — OCR & Intelligent Invoice Extraction

## Replacement scope
Replace `backend/invoice/extractor.py` with the supplied version and add the complete `backend/invoice/extraction/` package. Add `backend/tests/test_extraction_quality.py`.

## What this phase changes
- Keeps the existing OCR engines and file-format readers (Tesseract, PyMuPDF, pypdf, OpenCV, Pillow, Excel reader) intact.
- Adds a canonical post-extraction validation layer for product rows.
- Normalizes product name, quantity, unit price and line total without inventing missing values.
- Calculates line-item totals and reconciles them against invoice total with a documented tolerance.
- Emits explicit warnings for missing vendor/number/product rows/incomplete rows/total mismatch.
- Adds field provenance hints. Heuristic confidence is not claimed to be model accuracy.
- Preserves the original OCR text preview and OCR metadata returned by the existing extractor.

## Apply
1. Back up the project and database.
2. Apply Phase 01 first. Keep your `.env`, uploads, database and reports.
3. From this ZIP, replace `backend/invoice/extractor.py` and add `backend/invoice/extraction/` and the test file at the exact paths.
4. Do not delete Tesseract language data or OCR binaries.
5. Run from `backend/`: `python -m pytest tests/test_extraction_quality.py -q`.
6. Run `python -m compileall invoice/extractor.py invoice/extraction`.

## Important limitations
This phase improves validation and normalization after OCR; it does not magically guarantee perfect OCR on every layout. If a row is absent from the existing table extractor, it is reported as missing/incomplete rather than fabricated. Purchase date is only populated when present in source evidence. Market research and PDF generation are handled in later phases.
