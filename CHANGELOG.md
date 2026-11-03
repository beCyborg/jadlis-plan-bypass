# Changelog — jadlis-plan-bypass

Формат: [Keep a Changelog](https://keepachangelog.com/ru/1.1.0/), версии — [SemVer](https://semver.org/lang/ru/).

## [Unreleased]

## [1.0.0] — 2026-11-03 — Первый выпуск / First release

### Для человека

- План, одобренный с телефона через Remote Control, теперь ведёт себя как кнопка терминала «Yes, clear context and bypass permissions»: сессия уходит в bypass, контекст очищается, реализация начинается в чистом окне.
- Настройка `CLEAR_CONTEXT=no` оставляет только переключение в bypass.

### For agents

- Added: `hooks/hooks.json` — PostToolUse on `ExitPlanMode` → `scripts/plan-bypass-hook.sh` (timeout 20 s).
- Hook mode: reads the mode line under the prompt box of `$TMUX_PANE` (tty fallback); on `auto mode` cycles Shift+Tab until `bypass permissions`, writes a state file into `${CLAUDE_PLUGIN_DATA}/state`, schedules `--relaunch` via `tmux run-shell -b` (data dir passed as `PLAN_BYPASS_DATA`) and prints `{"continue": false, "stopReason": "plan-clear: …"}`.
- Relaunch mode: waits for the idle pane with the marker → `/clear` → re-checks bypass → types the implement-the-plan line with the transcript pointer → confirms the spinner.
- Option `CLEAR_CONTEXT` read from `CLAUDE_PLUGIN_OPTION_CLEAR_CONTEXT`; env `PLAN_CLEAR_ON_REMOTE=0`, `PLAN_BYPASS_MAX`, `PLAN_BYPASS_REMOTE_MODE` kept as test knobs. Log: `${CLAUDE_PLUGIN_DATA}/plan-bypass.log`.
