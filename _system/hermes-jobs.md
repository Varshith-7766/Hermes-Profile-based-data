---
tags: [hermes, system, cron]
updated: 2026-10-19
---

# Hermes — jobs & bridge (system record)

Where the automated jobs live. They are **not** inside Obsidian; Obsidian only
stores the notes. This file is just a record.

## Jobs

| Job | Runs every | Where it lives | What it does |
|---|---|---|---|
| `obsidian_watchdog.sh` | 5 min | VPS crontab (`crontab -l`) | Real write+read+delete round-trip on the bridge; Telegram alert with the cause if it fails |
| `HermesObsidianTunnel` | 5 min | Windows Task Scheduler | Starts the SSH tunnel if it is not already running (no duplicates) |
| `HermesObsidianVaultBackup` | 30 min | Windows Task Scheduler | Commits vault changes and pushes to GitHub (private) |

Crontab line:
`*/5 * * * * /home/ubuntu/.hermes/obsidian_watchdog.sh >> /tmp/obsidian_watchdog.log 2>&1`

## The bridge (how agents save here)

- Obsidian must be **open** with this vault active, and the **"Local REST API"** plugin enabled.
- SSH tunnel: laptop forwards → VPS `127.0.0.1:27123` (HTTP) / `27124` (HTTPS).
- MCP server `obsidian` in all 4 Hermes profiles talks to `http://127.0.0.1:27123/mcp/`.
- Each profile writes **only** to its own folder: `default/`, `researcher/`, `chatter/`, `tutor/`.

## Backups

- Local git repo in this vault; remote = `github.com/Varshith-7766/Hermes-Profile-based-data` (private).
- Auto-commit + push every 30 min. The API key file is gitignored (never leaves the machine).

## Maintenance notes

- If you toggle the "Local REST API" plugin, its API key regenerates → update
  `MCP_OBSIDIAN_API_KEY` in the 4 `.env` files on the VPS.
- Keep this vault active in Obsidian; switching to another vault makes the bridge serve that one instead.
- Failure alerts arrive on Telegram automatically; no action needed unless one persists.
