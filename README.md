# omarchy-pomodoro

A minimal pomodoro timer for the [Omarchy](https://omarchy.org) shell bar.

- One task per session: you name the one thing you're working on before you start
- 25 minutes of focus, then a 5-minute rest
- Ring countdown with four session dots that track your cycle for the day
- Parking lot: type distracting ideas into the popup and keep going; they're listed again when the session ends
- Optional work log: after each session, note what you got done
- Everything is stored locally in SQLite, and a running timer survives a shell restart
- Notifications when a session or rest ends

## Install

```bash
omarchy plugin add https://github.com/sarrietav-dev/omarchy-pomodoro.git --enable --yes
```

The widget lands in the right section of the bar; move it with `omarchy bar move`.
Plugins run unsandboxed inside `omarchy-shell`, so read the code first if you like
(it's two QML/JS files). Requires the `sqlite3` CLI.

## Use

- **Left click** the pill: open the popup. Type a task, press Enter.
- **Right click** the pill while a session runs: pause or resume.
- **Bind a key** to toggle the popup: `omarchy-shell shell toggle sebas.pomodoro '{}'`.

The pill shows a timer icon (idle), the countdown (focus), a coffee cup (rest), a pause icon
(paused), or a check mark (waiting for you to log a finished session).

## Settings

Set these on the widget's entry in `~/.config/omarchy/shell.json`:

| Key | Default | |
|---|---|---|
| `focusMinutes` | `25` | Focus length |
| `breakMinutes` | `5` | Rest length |
| `requireTask` | `true` | Refuse to start without a task name |
| `askLog` | `true` | Ask what you did after each session |

## Data

`~/.local/share/omarchy-pomodoro/pomodoro.db` (SQLite). Query it directly:

```bash
sqlite3 -header -column ~/.local/share/omarchy-pomodoro/pomodoro.db \
  "select task, (ended_at - started_at)/60 as minutes, status, note from sessions order by started_at desc limit 20"
```

Tables: `sessions`, `parked` (parking-lot thoughts), `kv` (the running timer), and
`sessions_remote`. The widget merges `sessions_remote` into today's list, so you can fill it
from other devices' logs if you want shared history; the plugin itself never writes to it.

## Development

The shell caches plugin QML, so run `omarchy-restart-shell` after editing. Validate with
`omarchy plugin validate .`.

## License

MIT
