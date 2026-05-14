Settings Keys
=============
Keys are stored in the SQLite settings table and are used by both the desktop
UI and the API (via /settings and /metadata). This list is grouped by feature.

ShotGrid config
- sg_url: ShotGrid base URL
- sg_script_name: ShotGrid script name
- sg_api_key: ShotGrid API key
- sg_sync_enabled: enable SG sync ("1"/"0")
- sg_offline_mode: offline flag ("1"/"0")
- sg_project_id: stored SG project id (fallback/default)
- sg_user_id: stored SG user id (fallback/default)
- sg_default_project_id: default SG project id for UI
- sg_default_user_id: default SG user id for UI
- sg_projects_json: list of projects (name/short/id) shown in Settings
- sg_users_json: list of SG users (name/id/role/all_projects)
- sg_project_tasks_json: per-project tasks, production tasks, and shotcodes
- sg_tasks_cache: per-project SG task cache (task labels + steps)
- sg_link_aliases_json: list of link aliases (alias/target/project) used for SG resolution

People and descriptions
- people: list_setting, local people list
- default_person: default person name
- descriptions: list_setting, saved description suggestions

Reminders and mini timer
- reminder_enabled
- reminder_work_start (HH:MM)
- reminder_work_end (HH:MM)
- reminder_inactive_minutes
- reminder_repeat_minutes
- reminder_snooze_minutes
- reminder_channels_json (list)
- tray_timer_notify_mode (off/once/always)
- tray_timer_notify_seconds
- tray_timer_notify_interval_seconds
- mini_pop_enabled
- mini_pop_interval_minutes
- longrun_enabled
- longrun_threshold_minutes
- longrun_confirm_timeout_minutes
- longrun_action

App UI and API
- tray_enabled
- run_on_startup
- ui_theme
- api_host
- api_port
- default_export_directory

Other list_settings
- pipeline_steps: list_setting, legacy pipeline steps list (mostly superseded by project tasks)

Where keys are read/written
- Desktop: main.py SettingsDialog.save + load_settings + load_projects
- Core/API: timelog_core/shotgrid_service.load_sg_config and backend/app/core.metadata
- API settings endpoint: backend/app/main.py /settings (PUT)
