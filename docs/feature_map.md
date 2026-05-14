Feature Map
===========
This is a quick index for where each major list or feature is populated and
which data sources it depends on. Desktop (PySide), API (FastAPI), and Web
(React) are all included.

Legend
- DB tables: projects, time_entries, settings
- Settings keys: see docs/settings_keys.md
- SG cache: MainWindow.sg_task_cache (persisted in settings key sg_tasks_cache)
- Assigned data: MainWindow.assigned_data_cache (runtime only)

Projects list
- Settings -> Projects/Tasks: main.py SettingsDialog._load_sg_projects_table
  Source order: settings sg_projects_json, then DB projects table merge.
  Persisted via SettingsDialog.save -> _collect_projects -> _sync_projects_into_db.
- Main Project selector: main.py MainWindow.load_projects
  Source: DB projects table (active). Seeds from settings via _ensure_projects_in_db_from_settings if empty.
- Manual Log dialog project combo: main.py MainWindow.show_manual_log_dialog
  Source: main window project_combo items; fallback to DB active projects.
- API metadata: backend/app/core.py metadata -> db.get_active_projects
- Web UI: frontend/app.js uses /metadata.projects

Tasks / pipeline list
- Settings -> Project Tasks: main.py SettingsDialog._load_project_tasks_inline
  Source: settings sg_project_tasks_json; can import from SG via _import_tasks_from_sg_inline.
- Main Task selector: main.py MainWindow.refresh_pipeline_for_current_mode
  Source order: assigned data cache (if enabled) -> SG cache -> project_tasks map (project_task_options).
- Manual Log dialog Task combo: main.py MainWindow.show_manual_log_dialog -> refresh_tasks
  Source: _valid_tasks_for_project (SG cache + project_tasks + DB time_entries fallback).
- Entries table Task quick-pick: main.py MainWindow.on_table_cell_clicked (column 1)
  Source: _valid_tasks_for_project.
- API metadata: backend/app/core.py load_pipeline_steps + project_tasks map
- Web UI: frontend/app.js pipelineEntries prefers assigned data, then tasks_cache, then project_tasks, then pipeline_steps

People list
- Settings -> Users: main.py SettingsDialog._load_sg_users_table
  Source: settings sg_users_json; fallback from settings default_person + list_setting people.
- Main Person selector: main.py MainWindow.refresh_person_combo
  Source: sg_config.user_list (from sg_users_json) then people list_setting.
- API metadata: backend/app/core.py load_people
- Web UI: frontend/app.js uses /metadata.people

Sequences / shots
- Desktop: main.py MainWindow.update_sequences_and_shots
  Source order: assigned_data_cache (if enabled) -> project_tasks.shotcodes.
- API metadata: backend/app/core.py load_sequences_and_shots (from sg_project_tasks_json)
- Web UI: frontend/app.js derives sequences from shots, falls back to /metadata.sequences

ShotGrid assigned data
- Desktop: main.py MainWindow._load_assigned_data (SG) + sg_task_cache persisted in settings sg_tasks_cache
- API: timelog_core/shotgrid_service.fetch_assigned_data (SG) updates sg_tasks_cache
- Web UI: frontend/app.js uses /shotgrid/assigned-data endpoints when assignedOnly is enabled

Entries
- Desktop: main.py MainWindow.load_entries_for_date + apply_entries_filter
  Source: DB time_entries
- Manual log creation: main.py MainWindow.show_manual_log_dialog -> db.insert_time_entry
- API: backend/app/core.row_to_entry -> /entries endpoints (in backend/app/main.py)
- Web UI: frontend/app.js uses /entries
