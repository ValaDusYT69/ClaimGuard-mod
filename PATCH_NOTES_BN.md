# ClaimGuard — Final Integrated Release Notes

## Phase 01 — Core Backend
- Runtime configuration, PostgreSQL connection lifecycle, startup initialization এবং readiness check এক জায়গায় আনা হয়েছে।
- `DATABASE_URL` ছাড়া normal runtime-এ নীরবে SQLite fallback হয় না।

## Phase 02 — OCR & Extraction Quality
- Existing OCR output-এর product rows normalize ও validate হয়।
- Missing fields, incomplete rows এবং invoice total mismatch warning আকারে আসে।
- Provenance ও completeness score heuristic; এগুলো OCR model accuracy নয়।

## Phase 03 — Market Intelligence
- Product-wise research, retailer/page evidence এবং model/variant checks যোগ হয়েছে।
- Unverified বা unrelated source থেকে market price তৈরি না করার guard রাখা হয়েছে।

## Phase 04 — Price Comparison & Risk Scoring
- প্রতিটি invoice line-এর benchmark আলাদা।
- পর্যাপ্ত independent retailer domain না থাকলে market average/price deviation দেখানো হয় না।
- Market price risk একটি explainable heuristic; fraud probability নয়।

## Phase 05 — PDF & Dashboard
- PDF report-এ extracted data, warnings/provenance, product-wise comparison এবং source evidence দেখানো হয়।
- User dashboard থেকে authenticated PDF download flow যোগ হয়েছে।

## Phase 06 — Integration & Cleanup
- Phase 01–05 source merge করে consolidated package তৈরি।
- Obsolete `backend/cg_patch/` archive ও generated caches বাদ।
- Project integrity regression test এবং consolidated Bengali installation guide যোগ।
- SearXNG-এর backend sample URL `8080` করা হয়েছে, included Docker Compose default port-এর সঙ্গে মিলিয়ে; real `backend/.env` পরিবর্তন করা হয়নি।

## Validation summary
- Active suite (`backend/tests/`) Phase 06-এর আগে 62 tests passed; final integrity tests যুক্ত হওয়ার পর আবার run করতে হবে।
- Python compileall এবং changed dashboard/details inline JavaScript syntax checks passed.
- Full unfiltered suite এই build environment-এ `pwdlib` অনুপস্থিত থাকায় collection stage-এ থেমেছে।
- Live PostgreSQL, SearXNG, retailers, browser download এবং real-invoice acceptance এই build run-এ পরীক্ষা করা হয়নি।
