---
weight: 7010
title: "curl"
description: "어디에나 있는 HTTP 클라이언트. 실무에서 실제로 쓰는 옵션만 추린 정리."
icon: "cable"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

`curl`은 거의 모든 서버에 이미 설치되어 있습니다. 그래서 "내 노트북에서는 되는데
서버에서는 안 된다"를 확인할 때 첫 번째로 꺼내는 도구입니다.

## 기본

```bash
curl https://api.example.com/users            # GET, 본문만 출력
curl -i https://api.example.com/users         # 응답 헤더 포함
curl -I https://api.example.com/users         # 헤더만 (HEAD)
curl -s https://api.example.com/users | jq    # 진행률 숨기고 jq로
curl -o users.json https://api.example.com/users   # 파일로 저장
```

## 자주 쓰는 옵션

| 옵션 | 의미 |
|---|---|
| `-X POST` | 메서드 지정 |
| `-H "키: 값"` | 헤더 추가 (여러 번 가능) |
| `-d '{"a":1}'` | 본문 전송 (지정 시 자동으로 POST) |
| `--data-urlencode` | 폼 값 인코딩 |
| `-F file=@a.png` | 멀티파트 파일 업로드 |
| `-u user:pass` | 기본 인증 |
| `-L` | 리다이렉트 따라가기 |
| `-s` / `-S` | 진행률 숨김 / 에러는 표시 |
| `-f` | HTTP 에러 시 종료 코드 실패로 (스크립트에 필수) |
| `-k` | 인증서 검증 생략 (디버깅 전용) |
| `--max-time 10` | 전체 타임아웃(초) |
| `-v` | 요청·응답 전 과정 출력 |

## JSON API 호출

```bash
curl -sS -X POST https://api.example.com/users \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{"name":"김철수","email":"kim@example.com"}' | jq
```

긴 본문은 파일로 빼는 편이 읽기 좋습니다.

```bash
curl -sS -X POST https://api.example.com/users \
  -H "Content-Type: application/json" \
  -d @payload.json | jq
```

## 디버깅

```bash
# 요청/응답 전체 흐름 보기
curl -v https://api.example.com/health

# 상태 코드만 확인
curl -s -o /dev/null -w "%{http_code}\n" https://example.com

# 구간별 소요 시간 측정
curl -s -o /dev/null -w \
  "dns:%{time_namelookup} connect:%{time_connect} tls:%{time_appconnect} total:%{time_total}\n" \
  https://example.com
```

느린 API의 원인이 DNS인지 TLS 핸드셰이크인지 서버 처리인지 이 한 줄로 갈립니다.

## 스크립트에서 안전하게

```bash
set -euo pipefail
curl -fsS --max-time 10 --retry 3 --retry-delay 2 \
  -H "Authorization: Bearer $TOKEN" \
  https://api.example.com/health
```

`-f`가 없으면 404 응답도 종료 코드 0으로 처리되어 실패를 놓칩니다. 자동화
스크립트에서는 반드시 붙이세요.

## 브라우저 요청 그대로 재현하기

크롬 개발자도구 → Network → 요청 우클릭 → `Copy as cURL`. 붙여넣으면 헤더와
쿠키까지 그대로 재현됩니다. 인증이 걸린 API를 디버깅할 때 가장 빠른 방법입니다.

**주의**: 이렇게 복사한 명령에는 세션 쿠키와 토큰이 들어 있습니다. 이슈나 채팅에
붙여넣기 전에 반드시 지우세요.

## 다음 단계

읽고 쓰기 편한 문법을 원한다면 → [HTTPie](/docs/network/httpie/)
