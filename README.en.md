[Русский](README.md) · English

# jadlis-plan-bypass

Approve a plan from your phone, and the session on your computer switches to bypass permissions, clears the context and starts implementing the plan in a clean context.

## Why

In the terminal, the plan dialog has "Yes, clear context and bypass permissions". The Remote Control app has one "approve" button. After it the session stays in auto mode, and the whole planning conversation stays in context.

The plugin fixes this without you. It fires right after a plan is approved:

1. It sees `auto mode on` on screen and presses Shift+Tab until `bypass permissions on` shows.
2. It stops the turn, so the model starts nothing in the old context.
3. It types `/clear` and then one line: "Implement the approved plan in <plan file>…" with a pointer to the full transcript.

If you approved the plan in the terminal itself, the plugin does nothing.

## Requirements

- macOS or Linux, `tmux`, `zsh` and `jq`.
- Claude Code runs inside tmux.
- Bypass is available in the session: `claude --dangerously-skip-permissions` or `"permissions": {"defaultMode": "bypassPermissions"}` in `~/.claude/settings.json`. Without it there is nothing to switch to, and the plugin stays silent.
- Remote Control, to approve plans from the phone.

## Installation

```
claude plugin marketplace add https://gitlab.com/beCyborg/jadlis-hub.git
claude plugin install jadlis-plan-bypass@jadlis
```

Then run `/reload-plugins` or restart Claude Code.

If you already have the same hook on `ExitPlanMode` in `settings.json`, remove it. Otherwise it fires twice.

## Settings

`/plugin configure jadlis-plan-bypass@jadlis`, field `CLEAR_CONTEXT`:

| Value | What happens |
|---|---|
| `yes` (default) | Switch to bypass, clear the context, implement in a clean context. |
| `no` | Only switch to bypass. Implementation continues in the same context. |

## How to check, and the log

Log: `~/.claude/plugins/data/jadlis-plan-bypass-jadlis/plan-bypass.log`. Every plan approval writes a `plan approved … screen='…'` line.

What the hook sees in a tmux pane: `"${CLAUDE_PLUGIN_ROOT}/scripts/plan-bypass-hook.sh" --probe <pane id>` (pane id: `tmux display-message -p '#{pane_id}'`).

## Limits

- The hook reads the mode from the line under the input box, by the English UI text (`auto mode on`, `bypass permissions on`). If Claude Code changes these words or the Shift+Tab mode order, the hook stops after a few presses and logs a warning.
- Outside tmux the hook does nothing.
- If the relaunch fails, the session waits with a `plan-clear:` message. Type "implement the plan" and work continues in the old context.
- Tested on Claude Code 2.1.28x.

<details>
<summary>Terms of use</summary>

No licence: all rights reserved by the author. Reading and personal use are fine. Commercial use and republishing by agreement.

</details>
