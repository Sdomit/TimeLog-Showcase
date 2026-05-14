Project purpose
Time Log application with shared core logic used by the desktop Qt UI and FastAPI/React layers.

Golden rules
Core business rules live in timelog_core so both the PySide desktop UI and the web UI stay in sync.
The FastAPI layer delegates to the same DatabaseManager/TimerService objects to avoid duplicate rules.
ShotGrid helpers remain in Python; the web/UI never reimplements ShotGrid logic in JavaScript.

File/module responsibilities
timelog_core/: shared core utilities with business logic and data access reused by desktop and web layers.
timelog_core/database.py: DatabaseManager (SQL + settings helpers).
timelog_core/shotgrid.py: ShotGrid import/availability helpers.
timelog_core/timer.py: TimerService encapsulating start/stop logic and DB writes.
timelog_core/utils.py: shared helpers (resource_path, sequence_from_token).
main.py: desktop app that imports timelog_core so core updates flow to API and web layers.
backend/app/main.py: FastAPI app for the Time Log API; serves the web UI from /ui when frontend exists.
backend/app/core.py: db_manager, timer_service, metadata, row_to_project, row_to_entry helpers.
backend/app/schemas.py: Pydantic BaseModel classes for API schemas.
frontend/index.html: loads React app scripts and styles.
frontend/app.js: React UI that calls the API for metadata, timer state, and entries.

Feature/data-source map (quick index)
Projects list (Settings -> Projects/Tasks): main.py SettingsDialog._load_sg_projects_table; sources are settings "sg_projects_json" with DB "projects" fallback; saved via SettingsDialog.save -> _collect_projects -> _sync_projects_into_db.
Main project selector: main.py MainWindow.load_projects; sources are DB "projects" (active), seeded from settings if empty via _ensure_projects_in_db_from_settings.
Main task selector (pipeline): main.py MainWindow.refresh_pipeline_for_current_mode; uses SG assigned cache if enabled, else per-project tasks from "sg_project_tasks_json" (project_task_options) plus SG cache if present.
Manual log dialog: main.py MainWindow.show_manual_log_dialog -> refresh_tasks -> _valid_tasks_for_project; sources are SG task cache, settings "sg_project_tasks_json", and DB "time_entries" fallback.
API metadata (web): backend/app/core.py metadata loads projects, people, pipeline_steps, sequences/shots, and project_tasks/tasks_cache via timelog_core/shotgrid_service.
See docs/feature_map.md for a full feature index, docs/data_sources.md for source precedence, and docs/settings_keys.md for setting keys.

Locked decisions
Existing SQLite database (timelog.db) is reused; no schema changes were made.
