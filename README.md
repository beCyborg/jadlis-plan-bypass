Русский · [English](README.en.md)

# jadlis-plan-bypass

Одобрил план с телефона — сессия на компьютере сама уходит в bypass permissions, очищает контекст и начинает реализацию плана в чистом окне.

## Зачем

В терминале после плана есть кнопка «Yes, clear context and bypass permissions». В приложении Remote Control кнопка одна — «одобрить». После неё сессия остаётся в auto mode, а весь разговор о плане остаётся в контексте.

Плагин исправляет это без твоего участия. Он срабатывает сразу после одобрения плана:

1. Видит на экране `auto mode on` и нажимает Shift+Tab, пока не появится `bypass permissions on`.
2. Останавливает ход, чтобы модель ничего не начала в старом контексте.
3. Набирает `/clear` и затем одну строку: «Implement the approved plan in <файл плана>…» со ссылкой на полную переписку.

Если ты одобрил план в самом терминале, плагин ничего не делает.

## Что нужно

- macOS или Linux, `tmux`, `zsh` и `jq`.
- Claude Code запущен внутри tmux.
- В сессии доступен bypass: `claude --dangerously-skip-permissions` или `"permissions": {"defaultMode": "bypassPermissions"}` в `~/.claude/settings.json`. Без этого переходить некуда, и плагин молчит.
- Remote Control, чтобы одобрять планы с телефона.

## Установка

```
claude plugin marketplace add https://github.com/beCyborg/jadlis-hub.git
claude plugin install jadlis-plan-bypass@jadlis
```

Потом набери `/reload-plugins` или перезапусти Claude Code.

Если у тебя уже есть такой же хук на `ExitPlanMode` в `settings.json`, убери его. Иначе он сработает дважды.

## Настройка

`/plugin configure jadlis-plan-bypass@jadlis`, поле `CLEAR_CONTEXT`:

| Значение | Что происходит |
|---|---|
| `yes` (по умолчанию) | Переключить в bypass, очистить контекст, начать реализацию в чистом окне. |
| `no` | Только переключить в bypass. Реализация идёт в том же контексте. |

## Как проверить и где лог

Лог: `~/.claude/plugins/data/jadlis-plan-bypass-jadlis/plan-bypass.log`. Каждое одобрение плана даёт строку `plan approved … screen='…'`.

Что видит хук в панели tmux: `"${CLAUDE_PLUGIN_ROOT}/scripts/plan-bypass-hook.sh" --probe <pane id>` (id панели — `tmux display-message -p '#{pane_id}'`).

## Ограничения

- Хук читает режим со строки под полем ввода, по английскому тексту интерфейса (`auto mode on`, `bypass permissions on`). Если Claude Code поменяет эти слова или порядок режимов по Shift+Tab, хук остановится после нескольких нажатий и запишет предупреждение в лог.
- Вне tmux хук ничего не делает.
- Если перезапуск не удался, сессия ждёт с сообщением `plan-clear:`. Напиши «реализуй план», и работа пойдёт в старом контексте.
- Проверено на Claude Code 2.1.28x.

<details>
<summary>Условия использования</summary>

Лицензии нет: все права сохранены за автором. Читать и пользоваться лично можно. Коммерческое использование и переиздание — по договорённости.

</details>
