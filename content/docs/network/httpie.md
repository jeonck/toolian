---
weight: 7020
title: "HTTPie"
description: "사람이 읽고 쓰기 좋은 문법의 HTTP 클라이언트. JSON 작업에서 특히 간결하다."
icon: "http"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

HTTPie는 curl과 같은 일을 하지만 문법이 짧고 출력이 자동으로 색칠·정렬됩니다.
JSON API를 손으로 두들겨 볼 때 타이핑 양이 절반 이하로 줄어듭니다.

## 설치

```bash
brew install httpie
pip install httpie
sudo apt install httpie
```

## 기본

```bash
http GET https://api.example.com/users
http https://api.example.com/users          # GET은 생략 가능
https example.com/users                     # https 스킴 축약
```

응답은 자동으로 문법 강조되고 JSON이 정렬됩니다. `jq`를 거치지 않아도 읽힙니다.

## 문법 규칙

| 기호 | 의미 | 예시 |
|---|---|---|
| `=` | JSON 문자열 필드 | `name=김철수` |
| `:=` | JSON 원시 타입 (숫자·불리언·배열) | `age:=30` `tags:='["a","b"]'` |
| `:` | 헤더 | `Authorization:"Bearer $TOKEN"` |
| `==` | 쿼리 스트링 | `page==2` |
| `@` | 파일 업로드 | `avatar@./me.png` |
| `=@` | 파일 내용을 문자열 필드로 | `bio=@bio.txt` |

## curl과 비교

```bash
# curl
curl -X POST https://api.example.com/users \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{"name":"김철수","age":30,"active":true}'

# HTTPie
http POST api.example.com/users \
  Authorization:"Bearer $TOKEN" \
  name=김철수 age:=30 active:=true
```

`Content-Type: application/json`은 자동으로 붙습니다.

## 자주 쓰는 옵션

| 옵션 | 의미 |
|---|---|
| `-v` | 요청과 응답 전체 표시 |
| `-h` | 응답 헤더만 |
| `-b` | 응답 본문만 |
| `--follow` | 리다이렉트 따라가기 |
| `-d` | 파일로 다운로드 |
| `-a user:pass` | 기본 인증 |
| `--session=이름` | 쿠키·헤더를 세션에 저장 |
| `--offline` | 실제 전송 없이 요청만 출력 (검증용) |

## 세션으로 인증 유지

```bash
http --session=prod POST api.example.com/login username=me password=secret
http --session=prod GET api.example.com/me      # 쿠키가 유지됨
```

세션 파일은 `~/.config/httpie/sessions/`에 저장됩니다. 비밀번호가 평문으로 남을 수
있으므로 공용 장비에서는 주의하세요.

## 언제 curl을 쓰나

| 상황 | 도구 |
|---|---|
| 내 장비에서 API를 탐색 | HTTPie |
| 운영 서버·컨테이너 안 | curl (이미 설치되어 있음) |
| CI 스크립트, 헬스체크 | curl (`-f` 종료 코드 처리) |
| 문서에 붙일 예제 | curl (범용성) |

## 다음 단계

요청을 저장하고 팀과 공유하려면 → [Bruno와 Postman](/docs/network/bruno-postman/)
