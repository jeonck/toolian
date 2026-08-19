---
weight: 9040
title: "cron과 launchd"
description: "정해진 시각에 스크립트를 돌리는 방법과, 조용히 실패하지 않게 만드는 요령."
icon: "schedule"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

백업, 로그 정리, 리포트 생성처럼 매일 같은 시각에 도는 작업은 스케줄러에
맡깁니다. 문제는 대부분의 스케줄 작업이 **조용히 실패한다**는 점입니다. 그것까지
포함해 정리합니다.

## cron (Linux, macOS)

```bash
crontab -e        # 편집
crontab -l        # 목록 확인
crontab -r        # 전체 삭제 (주의)
```

형식은 다섯 자리입니다.

```
분 시 일 월 요일  명령
*  *  *  *  *
```

| 표현 | 의미 |
|---|---|
| `0 3 * * *` | 매일 03:00 |
| `*/15 * * * *` | 15분마다 |
| `0 9 * * 1-5` | 평일 09:00 |
| `0 0 1 * *` | 매월 1일 자정 |
| `@reboot` | 부팅 시 |

예시:

```
0 3 * * * /usr/local/bin/backup.sh >> /var/log/backup.log 2>&1
```

## cron이 실패하는 세 가지 이유

1. **PATH가 다릅니다.** cron은 최소한의 환경에서 돕니다. `docker`, `node`,
   `python`이 "명령을 찾을 수 없음"으로 실패합니다. → **절대 경로를 쓰세요.**
   ```
   0 3 * * * /opt/homebrew/bin/docker system prune -af
   ```
2. **환경 변수가 없습니다.** `.zshrc`는 읽히지 않습니다. 스크립트 안에서 필요한
   변수를 직접 정의하거나 파일에서 읽어옵니다.
3. **출력이 사라집니다.** 리다이렉트를 안 하면 에러 메시지가 어디에도 안 남습니다.
   → 항상 `>> 로그파일 2>&1`을 붙입니다.

## launchd (macOS 권장)

macOS는 cron보다 launchd가 정식 방법입니다. 절전 중 놓친 작업을 깨어날 때
실행해 주는 장점도 있습니다.

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
launchctl start com.me.backup          # 즉시 실행해 테스트
launchctl unload ~/Library/LaunchAgents/com.me.backup.plist
```

## systemd timer (Linux 서버)

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

로그가 `journalctl`로 모이고, `Persistent=true`가 놓친 실행을 보정해 줍니다.
서버라면 cron보다 이쪽이 낫습니다.

## 실패를 알아채는 장치

```bash
#!/bin/bash
set -euo pipefail

trap 'curl -fsS -X POST "$SLACK_WEBHOOK" \
  -d "{\"text\":\"백업 실패: $(hostname)\"}"' ERR

/usr/local/bin/backup.sh
curl -fsS "$HEALTHCHECK_URL"     # 성공 시 헬스체크 핑
```

죽은 사람 스위치(dead man's switch) 방식이 특히 유용합니다. 성공했을 때만 핑을
보내고, 핑이 안 오면 알림이 오게 하면 **스크립트가 아예 실행되지 않은 경우**까지
잡아냅니다.

## 다음 단계

서비스끼리 연결하는 자동화가 필요하다면 → [n8n](/docs/automation/n8n/)
