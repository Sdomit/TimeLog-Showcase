# TimeLog V3.2

A production time-tracking application designed for VFX and animation studios. It integrates with [Autodesk ShotGrid](https://www.autodesk.com/products/shotgrid/overview) to let team members log hours directly against their assigned projects and tasks, with both a desktop GUI and a web-based interface sharing the same core logic.

---

## Features

- **Desktop app** — standalone Windows `.exe` built with PySide6 (Qt)
- **Web interface** — FastAPI backend + React frontend, served locally
- **ShotGrid integration** — fetch assigned projects, tasks, shots, and push time entries back to ShotGrid
- **Timer** — start/stop timer with automatic database writes
- **Manual logging** — log time for any date with project, task, description, and duration
- **Backfill** — fill in missing workdays automatically
- **Description suggestions** — auto-suggest previously used descriptions per project/task
- **End-of-day reminders** — configurable reminders with snooze and multiple notification channels
- **Multi-user support** — manage a list of people and switch active user
- **Offline mode** — full functionality without a ShotGrid connection; sync when available

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Desktop UI | Python 3.11, PySide6 (Qt6) |
| Web API | FastAPI, uvicorn |
| Web UI | React (CDN), plain CSS |
| Core logic | Shared `timelog_core` Python module |
| Database | SQLite (local, via `timelog.db`) |
| ShotGrid | `shotgun_api3` |
| Distribution | PyInstaller (Windows `.exe`) |

---

## Architecture

```
timelog_core/       ← shared business logic (used by desktop AND API)
  database.py       ← SQLite wrapper (time entries, projects, settings)
  shotgrid_service.py  ← ShotGrid API client and credential loader
  timer.py          ← timer start/stop logic
  backfill.py       ← missing-day backfill
  description_suggestions.py
  eod_reminder.py
  utils.py

main.py             ← PySide6 desktop application
backend/app/        ← FastAPI application
  main.py           ← API routes
  core.py           ← shared helpers (db_manager, timer_service, metadata)
  schemas.py        ← Pydantic models
frontend/           ← React web UI
```

Core rule: business logic lives once in `timelog_core` so any fix or change automatically applies to both the desktop and web layers.

---

## Configuration

All settings are stored in a local SQLite database. Credentials can also be supplied via environment variables (which take precedence):

| Variable | Description |
|----------|-------------|
| `SG_URL` | ShotGrid instance URL |
| `SG_SCRIPT` | ShotGrid script name |
| `SG_KEY` | ShotGrid API key |
| `SG_PROJECT_ID` | Default ShotGrid project ID |
| `SG_USER_ID` | Default ShotGrid user ID |

For a full list of settings keys, see [`docs/settings_keys.md`](docs/settings_keys.md).

---

## Running the Desktop App

```bash
# Install dependencies
py -3.11 -m pip install PySide6 shotgun_api3

# Launch
py main.py
```

Or use the pre-built Windows executable from the `dist/` folder.

---

## Running the Web Interface

```bash
# Install API dependencies
py -m pip install -r backend/requirements.txt

# Start the API server
uvicorn backend.app.main:app --reload

# Open in browser
# http://localhost:8000/ui       → web UI
# http://localhost:8000/docs     → Swagger API docs
```

---

## Key API Endpoints

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/metadata` | Projects, people, pipeline steps, tasks cache |
| `GET/POST` | `/entries` | List or create time entries |
| `PATCH/DELETE` | `/entries/{id}` | Edit or delete an entry |
| `POST` | `/timer/start` | Start the timer |
| `POST` | `/timer/stop` | Stop the timer and save entry |
| `GET` | `/timer` | Current timer state |
| `GET/POST` | `/projects` | List or manage projects |
| `GET/PUT` | `/settings` | Read or write settings |
| `POST` | `/shotgrid/test` | Test ShotGrid connectivity |
| `POST` | `/shotgrid/push-timelog` | Push a log entry to ShotGrid |

---

## Tests

```bash
py -m pip install -r tests/requirements.txt
pytest
```

Tests use a temporary copy of the database (via `TIMELOG_DB_PATH`) and do not modify live data.

CI helper: `.\ci.ps1`

---

## Documentation

- [`docs/CONTEXT.md`](docs/CONTEXT.md) — project structure and module responsibilities
- [`docs/feature_map.md`](docs/feature_map.md) — where each feature is implemented
- [`docs/data_sources.md`](docs/data_sources.md) — data source precedence (DB, settings, ShotGrid)
- [`docs/settings_keys.md`](docs/settings_keys.md) — full list of settings keys

---

## License

Private tool — not licensed for redistribution.
