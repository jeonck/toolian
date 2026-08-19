---
weight: 9040
title: "cron and launchd"
description: "Running scripts on a schedule — and making sure they don't fail silently."
icon: "schedule"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Backups, log rotation, report generation — anything that runs at the same time daily
belongs to a scheduler. The catch is that most scheduled jobs **fail silently**, so this
page covers that too.

## cron (Linux, macOS)

```bash
crontab -e        # edit
crontab -l        # list
crontab -r        # delete everything (careful)
```

The format is five fields:

```
min hour day month weekday  command
 *    *    *    *      *
```

| Expression | Meaning |
|---|---|
| `0 3 * * *` | Daily at 03:00 |
| `*/15 * * * *` | Every 15 minutes |
| `0 9 * * 1-5` | Weekdays at 09:00 |
| `0 0 1 * *` | Midnight on the first of the month |
| `@reboot` | At boot |

For example:

```
0 3 * * * /usr/local/bin/backup.sh >> /var/log/backup.log 2>&1
```

## Three reasons cron jobs fail

1. **PATH is different.** cron runs in a minimal environment, so `docker`, `node`, and
   `python` come back as "command not found." → **Use absolute paths.**
   ```
   0 3 * * * /opt/homebrew/bin/docker system prune -af
   ```
2. **Environment variables are missing.** `.zshrc` is never read. Define what you need
   inside the script, or read it from a file.
3. **Output disappears.** Without redirection, error messages go nowhere. → Always
   append `>> logfile 2>&1`.

## launchd (preferred on macOS)

launchd is the supported mechanism on macOS, and it will run a job you missed while the
machine was asleep.

`~/Library/LaunchAgents/com.me.backup.plist`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.me.backup</string>
    <key>ProgramArguments</key>
    <array>
        <string>/bin/bash</string>
        <string>/Users/me/scripts/backup.sh</string>
    </array>
    <key>StartCalendarInterval</key>
    <dict>
        <key>Hour</key><integer>3</integer>
        <key>Minute</key><integer>0</integer>
    </dict>
    <key>StandardOutPath</key>
    <string>/tmp/backup.log</string>
    <key>StandardErrorPath</key>
    <string>/tmp/backup.err</string>
</dict>
</plist>
```

```bash
launchctl load ~/Library/LaunchAgents/com.me.backup.plist
launchctl list | grep com.me.backup
launchctl start com.me.backup          # run now, to test
launchctl unload ~/Library/LaunchAgents/com.me.backup.plist
```

## systemd timers (Linux servers)

```ini
# /etc/systemd/system/backup.service
[Unit]
Description=Nightly backup

[Service]
Type=oneshot
ExecStart=/usr/local/bin/backup.sh
```

```ini
# /etc/systemd/system/backup.timer
[Unit]
Description=Run backup daily

[Timer]
OnCalendar=*-*-* 03:00:00
Persistent=true

[Install]
WantedBy=timers.target
```

```bash
sudo systemctl enable --now backup.timer
systemctl list-timers
journalctl -u backup.service -n 50
```

Logs collect in `journalctl`, and `Persistent=true` catches up missed runs. On a server
this beats cron.

## Noticing failures

```bash
#!/bin/bash
set -euo pipefail

trap 'curl -fsS -X POST "$SLACK_WEBHOOK" \
  -d "{\"text\":\"Backup failed on $(hostname)\"}"' ERR

/usr/local/bin/backup.sh
curl -fsS "$HEALTHCHECK_URL"     # ping only on success
```

The dead-man's-switch pattern is the valuable one. Ping only on success and alert when
the ping doesn't arrive, and you also catch **the case where the script never ran at
all**.

## Next

To connect services to each other → [n8n](/docs/automation/n8n/)
