---
weight: 7040
title: "로컬 서버 외부 노출"
description: "웹훅 테스트와 모바일 확인을 위해 localhost를 임시 공개 URL로 여는 방법."
icon: "swap_horiz"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

결제사 웹훅을 받아봐야 하거나, 휴대폰으로 개발 중인 화면을 확인해야 할 때가
있습니다. 배포하지 않고도 `localhost:3000`을 공개 주소로 잠깐 여는 도구들입니다.

## ngrok

가장 널리 쓰입니다. 계정과 인증 토큰이 필요합니다.

```bash
brew install ngrok
ngrok config add-authtoken <토큰>
ngrok http 3000
```

실행하면 `https://xxxx.ngrok-free.app` 형태의 주소가 뜹니다. 이 주소를 웹훅
설정에 넣으면 됩니다.

`http://localhost:4040`에서 지나간 요청의 헤더와 본문을 전부 볼 수 있고, **같은
요청을 다시 보내는(replay)** 기능이 있습니다. 웹훅 디버깅에서 이 기능이 가장
유용합니다 — 결제를 다시 하지 않아도 같은 이벤트를 반복 재현할 수 있습니다.

## cloudflared

Cloudflare 계정만 있으면 되고, 임시 터널은 로그인 없이도 됩니다.

```bash
brew install cloudflared
cloudflared tunnel --url http://localhost:3000
```

고정 주소가 필요하면 이름 있는 터널을 만듭니다.

```bash
cloudflared tunnel login
cloudflared tunnel create dev
cloudflared tunnel route dns dev dev.example.com
cloudflared tunnel run dev
```

## SSH 역터널

서버 한 대만 있으면 추가 도구 없이 됩니다.

```bash
ssh -R 8080:localhost:3000 user@myserver.com
```

서버의 `sshd_config`에 `GatewayPorts yes`가 있어야 외부에서 접근됩니다.

## 비교

| | ngrok | cloudflared | SSH 역터널 |
|---|---|---|---|
| 준비물 | 계정 | Cloudflare 계정(고정 주소 시) | 공인 IP 서버 |
| 요청 검사 UI | 있음 (강력) | 없음 | 없음 |
| 고정 도메인 | 유료 | 무료(도메인 보유 시) | 서버 도메인 사용 |
| 설정 난이도 | 가장 쉬움 | 중간 | 중간 |

## 보안 주의사항

터널을 열면 **인터넷 전체가 내 로컬 서버에 접근할 수 있습니다.**

- 개발 DB에 실제 고객 데이터를 넣어두지 마세요.
- 디버그 엔드포인트, 관리자 페이지가 열려 있지 않은지 확인합니다.
- 필요한 시간만 열고 즉시 닫습니다. 백그라운드에 방치하지 마세요.
- ngrok은 `--basic-auth "user:pass"`로 간단한 보호를 걸 수 있습니다.
- 공유한 주소는 만료 후에도 로그에 남습니다. 민감한 경로를 URL에 넣지 마세요.

## 다음 단계

이제 배포 쪽으로 넘어갑니다 → [컨테이너 & 배포](/docs/devops/)
