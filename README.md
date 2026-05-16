<div align="center">

# TimeLog

**VFX and animation studio time-tracking with ShotGrid integration — desktop and web, shared core.**

[![Platform: Windows](https://img.shields.io/badge/Platform-Windows-0078D6?style=flat-square&logo=windows)]()
[![Stack: Python / PySide6 / FastAPI / React](https://img.shields.io/badge/Stack-Python%20%2F%20PySide6%20%2F%20FastAPI%20%2F%20React-3776AB?style=flat-square)]()
[![Showcase](https://img.shields.io/badge/Source-Private%20Showcase-lightgrey?style=flat-square)]()

> Private source — this repository is a project showcase.

</div>

---

## What It Does

TimeLog is a production time-tracking application for VFX and animation studios. Team members log hours directly against their assigned ShotGrid projects and tasks — from either a native Windows desktop app or a locally-served web interface, both powered by the same core logic.

---

## Features

- **Desktop app** — standalone Windows `.exe` built with PySide6 (Qt6)
- **Web interface** — FastAPI backend + React frontend, served locally
- **ShotGrid integration** — fetch assigned projects, tasks, and shots; push time entries back to ShotGrid
- **Timer** — start/stop with automatic database writes
- **Manual logging** — log time for any date with project, task, description, and duration
- **Backfill** — fill in missing workdays automatically
- **Description suggestions** — auto-suggest previously used descriptions per project/task pair
- **End-of-day reminders** — configurable with snooze and multiple notification channels
- **Multi-user support** — manage a list of people and switch active user
- **Offline mode** — full functionality without a ShotGrid connection; sync when available

---

## Tech Stack

| Layer | Technology |
|:---|:---|
| Desktop UI | Python 3.11, PySide6 (Qt6) |
| Web API | FastAPI, Uvicorn |
| Web UI | React (CDN), plain CSS |
| Core logic | Shared `timelog_core` Python module |
| Database | SQLite (`timelog.db`) |
| ShotGrid | `shotgun_api3` |
| Distribution | PyInstaller (Windows `.exe`) |

---

## Architecture

```
timelog_core/                  shared business logic (desktop + API)
  database.py                  SQLite wrapper — time entries, projects, settings
  shotgrid_service.py          ShotGrid API client and credential loader
  timer.py                     timer start/stop logic
  backfill.py                  missing-day backfill
  description_suggestions.py
  eod_reminder.py
  utils.py

main.py                        PySide6 desktop application
backend/app/                   FastAPI application
  main.py                      API routes
  core.py                      shared helpers (db_manager, timer_service)
  schemas.py                   Pydantic models
frontend/                      React web UI
```

Core rule: business logic lives once in `timelog_core` so any fix applies to both desktop and web simultaneously.

---

## Configuration

Credentials can be supplied via environment variables (which take precedence over database settings):

| Variable | Description |
|:---|:---|
| `SG_URL` | ShotGrid instance URL |
| `SG_SCRIPT` | ShotGrid script name |
| `SG_KEY` | ShotGrid API key |
| `SG_PROJECT_ID` | Default project ID |
| `SG_USER_ID` | Default user ID |

---

## Key API Endpoints

| Method | Path | Description |
|:---|:---|:---|
| `GET` | `/metadata` | Projects, people, pipeline steps, tasks cache |
| `GET/POST` | `/entries` | List or create time entries |
| `PATCH/DELETE` | `/entries/{id}` | Edit or delete an entry |
| `POST` | `/timer/start` | Start the timer |
| `POST` | `/timer/stop` | Stop the timer and save entry |
| `GET/PUT` | `/settings` | Read or write settings |
| `POST` | `/shotgrid/push-timelog` | Push a log entry to ShotGrid |

---

## Status

Active development. Private source — this repository is a project showcase.
