# LPU Multi-Department Event Scheduling & Resource Conflict Resolution System

[![Python 3.11](https://img.shields.io/badge/Python-3.11-blue.svg)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.115.0-009688.svg)](https://fastapi.tiangolo.com/)
[![Scikit-Learn](https://img.shields.io/badge/scikit--learn-1.9.1-orange.svg)](https://scikit-learn.org/)
[![Google OR-Tools](https://img.shields.io/badge/OR--Tools-CP--SAT-red.svg)](https://developers.google.com/optimization)
[![Tests Passing](https://img.shields.io/badge/pytest-22%20passed-brightgreen.svg)]()

> **Branded for Lovely Professional University (LPU)**  
> **Tagline**: *Constraint Satisfaction • Heuristic Search • Priority Rules • Explainable AI*

---

## 1. Project Directory Tree

```
university_event_scheduler/
├── app/+7
│   ├── __init__.py
│   ├── main.py                  # FastAPI server, REST routes, Swagger /docs, CORS
│   ├── db.py                    # SQLite engine, schema migration, full CRUD helpers
│   ├── models.py                # Pydantic v2 schemas (Entities, Results, What-If, Analytics)
│   ├── demo.py                  # Seed demo seeder: 3 depts, 7 events, 4 venues, 3 staff, 3 equip
│   ├── scheduler.py             # Custom CSP Solver (MRV, Degree, Priority, Buffer, Explainability)
│   ├── ortools_solver.py        # Google OR-Tools CP-SAT cross-check & benchmark engine
│   ├── auth.py                  # Role-based access control (Admin, Coordinator, Viewer)
│   ├── export.py                # Schedule exporter (CSV, ReportLab PDF, iCalendar .ics)
│   ├── ml/
│   │   ├── __init__.py
│   │   ├── dataset.py           # Synthetic academic event generator (1,200 records)
│   │   ├── train.py             # Pipeline trainer for 3 Scikit-Learn models
│   │   ├── models.py            # Inference wrapper for attendance, conflict-risk & slot scoring
│   │   └── artifacts/           # Serialized joblib model weights
│   ├── static/
│   │   ├── app.js               # Reactive client application (Timetable, What-If, Modals, Themes)
│   │   ├── style.css            # Multi-theme CSS (LPU Light, Midnight Blue, Cyber Purple, Emerald)
│   │   ├── logo.png             # LPU university crest logo
│   │   ├── campus.png           # LPU campus landmark photo
│   │   └── images.jpg           # University campus facility image
│   └── templates/
│       └── index.html           # Single-page interface matching reference photo layouts
├── data/
│   ├── demo_data.json           # Portable JSON seed dataset (3-7-4-3-3 demo scenario)
│   └── synthetic_training.csv   # Documented synthetic training dataset
├── docs/
│   ├── architecture.md          # Full architectural flowcharts, equations & CSP formulation
│   └── viva_presentation.md     # Viva defense guide, Q&A, trade-off analysis
├── tests/
│   ├── test_scheduler.py        # Unit tests for hard constraints, priority rules, buffers, edge cases
│   ├── test_ml.py               # Unit tests for ML dataset generation, regression & classification
│   └── test_api.py              # Integration tests for FastAPI endpoints, CRUD, exports, and auth
├── Dockerfile                   # Production Python 3.11 container image
├── docker-compose.yml           # Multi-container orchestration
├── requirements.txt             # Locked dependencies
└── README.md                    # Comprehensive documentation and demo walkthrough
```

---

## 2. System Overview & Core Features

1. **Load Demo Data**:
   - Seeds exactly **3 departments**, **7 events**, **4 venues**, **3 staff members**, and **3 equipment types** into SQLite.
   - Includes deliberate, realistic academic boundary conflicts (capacity overflow, simultaneous staff contention, equipment shortages).
2. **AI Scheduling Engine (CSP + Heuristics)**:
   - Evaluates hard constraints (capacity, venue type, availability windows, buffer times, staff & equipment non-overlap).
   - Optimizes soft preferences (preferred venue, preferred start hour, avoiding late sessions, load balancing).
   - Guided by **Priority Tiering** (Critical > High > Normal > Low), **Minimum Remaining Values (MRV)**, and the **Degree Heuristic**.
3. **Google OR-Tools CP-SAT Cross-Check**:
   - Formulates a parallel integer programming model using `ortools.sat.python.cp_model` to benchmark assignment agreement and solver runtime.
4. **Machine Learning Pipeline (Scikit-Learn)**:
   - **Attendance Predictor**: Predicts realistic event turnout to detect venue overflow before it happens.
   - **Conflict-Risk Classifier**: Scores events (`Low`, `Medium`, `High`) based on resource scarcity indices.
   - **Learned Slot Preference Model**: Translates historical student/faculty convenience patterns into dynamic soft costs.
   - *Transparency Note*: As requested, the training data (`data/synthetic_training.csv`) is synthetically generated to model realistic university attendance distributions without exposing private student records.
5. **Explainable AI (XAI)**:
   - Explains every assignment in plain English: "Why this venue/time?", "Which hard rules were satisfied?", "Which soft preferences were sacrificed?", and "Who won the conflict and why?".
   - Computes nearest alternative slots/venues for unplaced events.
6. **What-If Scenario Simulator**:
   - Coordinators can adjust priorities or venue capacities on the fly; the engine re-solves in milliseconds and displays an instant side-by-side before/after diff.
7. **Full REST CRUD & Export**:
   - Full REST CRUD for `/departments`, `/venues`, `/equipment`, `/staff`, `/events`.
   - One-click export to **CSV**, **ReportLab PDF** with LPU branding, and **iCalendar (.ics)** for Google/Outlook calendars.
8. **Multi-Theme Interface**:
   - Directly matches the 4 user reference designs:
     1. **LPU Classic Light** (Default): Crisp white cards, orange accents, curved campus photo with *"Learn • Collaborate • Innovate"*.
     2. **Midnight Deep Blue**: Navy dark mode with glowing cyan badges.
     3. **Cyber Sunset Purple**: Indigo/purple gradient with glowing cards.
     4. **Emerald Eco-Campus**: Fresh sage green with natural university palette.

---

## 3. Quick Start & Execution

### Option A: Local Python Environment
```bash
# 1. Clone or navigate to the directory
cd university_event_scheduler

# 2. Activate virtual environment (Windows)
.ML\Scripts\activate
# Or create a new one:
# python -m venv .venv && .venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Run database migrations & seed demo
python -c "from app.db import init_db; from app.demo import load_demo; init_db(); load_demo()"

# 5. Launch the FastAPI server
uvicorn app.main:app --reload --port 8000
```
Open **[http://127.0.0.1:8000](http://127.0.0.1:8000)** in your web browser.  
Interactive Swagger API docs are available at **[http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs)**.

### Option B: Docker / Docker Compose
```bash
docker-compose up --build
```
Open **[http://localhost:8000](http://localhost:8000)**.

---

## 4. Running Automated Tests

Run the complete 22-test automated suite (scheduler, ML, and REST API):
```bash
python -m pytest -v
```

Expected output:
```
tests/test_api.py ........                                               [ 36%]
tests/test_ml.py ....                                                    [ 54%]
tests/test_scheduler.py ..........                                       [100%]

============================= 22 passed in 12.30s ==============================
```

---

## 5. Sample Demo Scenario: Conflict Resolution & Explainability

### Deliberate Clash 1: Staff & Equipment Contention
- **Scenario**: Both `AI/ML Research Symposium` (CSE Department) and `Startup Pitch Day` (MBA Department) request Day 0 at 10:00 AM.
- **Resource Bottleneck**: Both events require **Mr. Bob (AV Technician)** and **Portable PA Sound System** (university stock: 1 unit).
- **Solver Resolution**:
  - `AI/ML Research Symposium` has priority **Critical**, while `Startup Pitch Day` has priority **High**.
  - Priority Ordering ensures the Critical symposium is placed first in Central Auditorium.
  - Startup Pitch Day is safely shifted, and the Explainable AI panel logs:
    > *"Won by AI/ML Research Symposium due to priority precedence. Mr. Bob and Portable PA System already reserved."*

### Deliberate Clash 2: Physical Seating Capacity Overflow
- **Scenario**: `500-Seat Mega Orientation` requests an expected attendance of 500 in Central Auditorium.
- **Resource Bottleneck**: Central Auditorium physical capacity is 300.
- **Solver Resolution**:
  - The static candidate pruner catches the overflow immediately before search:
    > *"Attendance overflow: Event requires 500 seats, but university's largest venue has capacity 300."*

---

## 6. Viva / Defense Presentation Summary

- **Problem**: University resource scheduling is an NP-complete combinatorial problem with coupled constraints (rooms, faculty, hardware, timing preferences).
- **Approach**: Formulated as a discrete CSP with Backtracking, Forward Checking, MRV, Degree Heuristic, and Priority Rules, cross-referenced with Google OR-Tools CP-SAT.
- **Why CSP**: Guarantees zero hard constraint violations, operates deterministically, runs in $< 20$ms on university-scale datasets, and naturally generates transparent explainability reports.
- **Results**: Successfully resolves overlapping resource contention, achieves 100% hard constraint satisfaction, and provides real-time What-If schedule diffing.
- **Future Work**: Integration with university single sign-on (SSO / LDAP), real-time faculty calendar sync via Microsoft Graph / Google Calendar API, and multi-campus shuttle transit constraints.
