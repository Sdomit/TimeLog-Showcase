Data Sources and Precedence
===========================
This app mixes local DB data, settings, and ShotGrid lookups. This doc lists
sources and the usual precedence order for common lists.

SQLite tables
- projects: primary project list for desktop and API. Active projects are used
  for selectors; settings can seed/refresh this table.
- time_entries: stored logs used by entries view and used as a fallback source
  for task labels when no SG/settings tasks exist.
- settings: key/value store used by both desktop and API. See docs/settings_keys.md.

Settings-backed JSON blobs
- sg_projects_json: settings copy of projects list (name/short/id). Used by
  Settings dialog and to seed DB projects.
- sg_users_json: ShotGrid user list (name/id/role). Used for People list.
- sg_project_tasks_json: per-project tasks, production tasks, and shotcodes.
- sg_tasks_cache: per-SG-project task cache (task labels + steps) from SG.

In-memory caches (desktop)
- MainWindow.project_map: active projects from DB keyed by id.
- MainWindow.project_tasks: in-memory copy of sg_project_tasks_json plus merges
  from SG cache (apply_cached_tasks_for_project).
- MainWindow.sg_task_cache: task cache keyed by SG project id (mirrors sg_tasks_cache).
- MainWindow.assigned_data_cache: assigned SG tasks/shots for current user/project.

Source precedence (common lists)
- Task list (manual log + task selector): assigned_data_cache (if enabled)
  -> sg_tasks_cache -> sg_project_tasks_json -> DB time_entries fallback.
- Project list (main selector): DB projects (active) -> settings sg_projects_json seed.
- People list: settings sg_users_json -> list_setting people -> default_person.
- Shots/sequences: assigned_data_cache (if enabled) -> sg_project_tasks_json shotcodes.

ShotGrid data
- Assigned projects/tasks/shots come from SG when sync is enabled and offline mode is off.
- SG data is cached in sg_tasks_cache and reflected in project_tasks when available.

API metadata
- backend/app/core.py metadata collects projects, people, pipeline_steps,
  sequences/shots, project_tasks map, and tasks_cache for the web UI.
