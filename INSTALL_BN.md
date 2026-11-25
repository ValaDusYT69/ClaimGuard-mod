# ClaimGuard — Final Clean Project: বাংলা Installation Guide

এই ZIP-এ Phase 01–05-এর পরিবর্তন একসঙ্গে merge করা আছে। Phase 01, 02, 03, 04, 05 আলাদা করে apply করতে হবে না, যদি এই **Final Clean Project ZIP** দিয়ে নতুন clean folder তৈরি করো। Existing project update করলে নিচের backup সতর্কতা অবশ্যই মানবে।

## ১. আগে backup

- পুরো বর্তমান ClaimGuard project folder-এর backup নাও।
- PostgreSQL database-এর verified backup নাও।
- `backend/.env` এবং `searxng/.env` আলাদা নিরাপদ স্থানে রাখো।
- `backend/data/`-এর legacy JSON/SQLite data, `backend/uploads/`, `backend/reports/` এবং OCR assets থাকলে সেগুলোও আলাদা backup নাও।
- এই ZIP-এ কোনো user data বা real secret নেই; backup ছাড়া পুরোনো project-এর ওপর overwrite করবে না।

## ২. Clean folder তৈরি

ZIP extract করে নতুন folder-এ রাখো। Windows PowerShell থেকে project root-এ:

```powershell
py -3.12 -m venv .venv
.venv\Scripts\python.exe -m pip install --upgrade pip
.venv\Scripts\python.exe -m pip install -r backend\requirements.txt
```

Python version তোমার installed package compatibility অনুযায়ী বেছে নাও। Dependency installation fail করলে error log রেখে dependency issue resolve না করে production-এ deploy করবে না।

## ৩. Environment configure

`backend/.env.example` কপি করে `backend/.env` বানাও। PostgreSQL connection string এবং কমপক্ষে 32-character random `SECRET_KEY` বসাও। নিজের frontend origin `ALLOWED_ORIGINS`-এ দাও। Email workflow ব্যবহার করলে SMTP credentials বসাও। OCR ও market search-এর জন্য আলাদা section-এ দেওয়া settings configure করো।

`searxng/.env.example` শুধু SearXNG চালাতে হলে কপি করে `searxng/.env` বানাও। `searxng/core-config/settings.yml`-এর `server.secret_key`-এ আলাদা random secret বসাও; placeholder রেখে চালাবে না। Docker Compose-এর default port `8080`; তাই backend sample URL-ও `http://127.0.0.1:8080`। SearXNG optional; configured, authorized instance ছাড়া random public instance ধরে নেওয়া হয় না।

## ৪. PostgreSQL

আগে PostgreSQL server, database এবং application role প্রস্তুত করো। Existing SQLite/JSON data থাকলে সরাসরি নতুন database-এ copy/overwrite করবে না। `docs/POSTGRESQL_MIGRATION_WINDOWS.md`-এর backup, migration report ও row-count verification ধাপ অনুসরণ করো।

## ৫. OCR engine

Tesseract native application install করতে হবে; শুধু Python package যথেষ্ট নয়। `ben` এবং `eng` traineddata থাকতে হবে। যাচাই:

```powershell
tesseract --version
tesseract --list-langs
```

`ben` ও `eng` না থাকলে OCR চালানোর আগে language data install করো। বিস্তারিত: `docs/OCR_SETUP_WINDOWS.md`।

## ৬. Backend চালানো

```powershell
Push-Location backend
..\.venv\Scripts\python.exe -m uvicorn main:app --reload --host 127.0.0.1 --port 8000
Pop-Location
```

তারপর `GET http://127.0.0.1:8000/api/health/live` এবং `GET http://127.0.0.1:8000/api/health/ready` পরীক্ষা করো। Ready endpoint database connectivity-ও যাচাই করে।

Frontend pages `frontend/`-এ আছে। Static frontend server থেকে চালালে সেই exact origin `ALLOWED_ORIGINS`-এ যোগ করতে হবে।

## ৭. Tests

```powershell
Push-Location backend
$env:PYTHONPATH = "."
..\.venv\Scripts\python.exe -m pytest tests/ -q
Pop-Location
```

সব dependency install করার পর full suite চালাবে। শুধু targeted test pass করলেই live PostgreSQL, SearXNG, retailer websites, browser flow বা production OCR validated হয়ে যায় না।

## ৮. পুরোনো project থেকে upgrade

এই clean ZIP থেকে existing production folder-এ blind overwrite করবে না। Phase 01–05 replacement chain আগে apply করে থাকলে Phase 06 package-এর নির্দেশনা অনুসরণ করে legacy archive/cache cleanup করতে পারো। Existing `.env`, database, `backend/data/`, uploads, reports, OCR assets এবং user data preserve করবে। Migration acceptance শেষ না হওয়া পর্যন্ত পুরোনো working copy delete করবে না।
