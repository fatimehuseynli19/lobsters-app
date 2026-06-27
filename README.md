# Lobsters Top Posts — Team Project

## What we're building

A small full-stack app that shows the **top 10 hottest posts from Lobsters** (lobste.rs),
a programming-focused link aggregator.

```
Lobsters' public JSON API
        │
        ▼
┌─────────────────────┐
│   DATA PIPELINE      │   ← Data Engineer
│  (fetch → transform   │
│   → load into DB)     │
└──────────┬───────────┘
           ▼
┌─────────────────────┐
│   lobsters.db           │   ← Shared SQLite database
│   (SQLite file)        │
└──────────┬───────────┘
           ▼
┌─────────────────────┐
│   BACKEND API          │   ← Backend Developer
│  (Flask + SQLAlchemy)  │
└──────────┬───────────┘
           ▼  HTTP / JSON
┌─────────────────────┐
│   FRONTEND              │   ← Frontend Developer
│  (HTML + CSS + JS)      │
└─────────────────────┘
```

**The golden rule is the same as always: each layer only talks to the layer directly
next to it.** The frontend never touches the database. The pipeline never touches HTTP.
The backend never calls Lobsters directly.

---

## Folder structure

```
lobsters-app/
│
├── data-pipeline/              ← DATA ENGINEER's folder
│   ├── pipeline/
│   │   ├── models.py           (schema — source of truth)
│   │   ├── db.py                (database connection)
│   │   ├── fetcher.py           (Layer 1: talk to Lobsters)
│   │   ├── transformer.py       (Layer 2: clean the data)
│   │   └── loader.py            (Layer 3: save to database)
│   ├── tests/
│   │   ├── test_fetcher.py
│   │   ├── test_transformer.py
│   │   ├── test_loader.py
│   │   └── sample_lobsters_response.json
│   ├── run_pipeline.py          (entry point — run this!)
│   └── requirements.txt
│
├── backend/                    ← BACKEND DEVELOPER's folder
│   ├── app/
│   │   ├── models.py            (schema — must match pipeline's!)
│   │   ├── db.py                (database connection, read-only)
│   │   ├── repository.py        (Layer 1: database queries)
│   │   ├── service.py           (Layer 2: business logic)
│   │   └── routes.py            (Layer 3: Flask HTTP endpoints)
│   ├── tests/
│   │   ├── test_repository.py
│   │   ├── test_service.py
│   │   └── test_routes.py
│   ├── run.py                   (entry point — run this!)
│   └── requirements.txt
│
├── frontend/                   ← FRONTEND DEVELOPER's folder
│   ├── index.html               (page structure)
│   ├── style.css                (already done for you!)
│   └── app.js                   (fetch + render logic)
│
├── lobsters.db                   ← created automatically, shared by pipeline + backend
│
└── docs/                       ← read these first!
    ├── ARCHITECTURE.md          (how the 3 parts fit together)
    ├── DATA_ENGINEER.md         (guide for the data pipeline)
    ├── BACKEND.md                (guide for the Flask API)
    └── FRONTEND.md               (guide for the HTML/CSS/JS)
```

---

## Setup (everyone do this first)

You need **Python 3.10+** installed.

```bash
# From inside data-pipeline/
cd data-pipeline
pip install -r requirements.txt

# From inside backend/
cd ../backend
pip install -r requirements.txt
```

The frontend needs no installation — it's plain HTML/CSS/JS, opened directly in a browser
or served with a simple local server.

---

## Build order (this matters!)

Work in this order, because each part depends on the one before it:

1. **Data Engineer** defines the schema in `pipeline/models.py` and shares it with
   the Backend Developer (who copies it into `backend/app/models.py`).
2. **Data Engineer** builds the pipeline (`fetcher.py` → `transformer.py` → `loader.py`)
   and runs it once to create `lobsters.db` with real data.
3. **Backend Developer** builds the API (`repository.py` → `service.py` → `routes.py`)
   and starts the Flask server.
4. **Frontend Developer** builds `app.js` to call the running API and display results.

You CAN work in parallel once the schema (step 1) is agreed on — the Backend Developer
can build against a manually-seeded test database while the pipeline is still being
finished (see `backend/tests/test_repository.py` for an example of seeding test data).

---

## Running everything together

```bash
# 1. Run the pipeline once to populate the database
cd data-pipeline
python run_pipeline.py

# 2. Start the backend API (leave this running in its own terminal)
cd ../backend
python run.py
# API is now live at http://localhost:5000

# 3. Open the frontend
cd ../frontend
# Just open index.html directly in your browser, OR serve it:
python -m http.server 8000
# then visit http://localhost:8000
```

---

## Next steps

Each specialization has its own detailed guide in `docs/`:

- 📡 [`docs/DATA_ENGINEER.md`](docs/DATA_ENGINEER.md)
- 🔧 [`docs/BACKEND.md`](docs/BACKEND.md)
- 🎨 [`docs/FRONTEND.md`](docs/FRONTEND.md)
- 🏗️ [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) — read this one as a whole team together first!

## Contributors

- @fatimehuseynli19 — Data Pipeline
- @sevincmamedova — Backend API  
- @Ayan1413 — Frontend UI
