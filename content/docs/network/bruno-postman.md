---
weight: 7030
title: "Bruno와 Postman"
description: "API 요청을 컬렉션으로 저장하고 팀과 공유하는 GUI 클라이언트 두 가지."
icon: "folder_shared"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

curl 명령을 슬랙에 붙여 공유하는 단계를 넘어서면, 요청 묶음을 저장하고 환경별로
바꿔 쓸 도구가 필요합니다. 선택지는 크게 둘입니다.

## 무엇이 다른가

| | Bruno | Postman |
|---|---|---|
| 저장 방식 | 파일(.bru)로 저장 → Git으로 관리 | 클라우드 워크스페이스 |
| 계정 | 불필요 | 필요(협업 기능) |
| 협업 | Git 브랜치·PR 그대로 | 웹 기반 공유·권한 관리 |
| 기능 폭 | 핵심에 집중 | 모니터링, 목 서버, 문서화까지 |
| 적합한 경우 | 저장소와 함께 버전 관리 | 비개발 직군 포함한 대규모 협업 |

코드 저장소에 요청을 함께 커밋하고 싶다면 Bruno, 조직 전체가 쓰는 API 포털이
필요하면 Postman이 무난합니다.

## Bruno 시작하기

```bash
brew install --cask bruno
```

컬렉션을 만들면 폴더와 `.bru` 파일이 생깁니다. 이걸 그대로 저장소에 커밋합니다.

```
api/
  bruno.json
  environments/
    local.bru
    prod.bru
  users/
    list-users.bru
    create-user.bru
```

`.bru` 파일은 텍스트라 diff가 읽힙니다.

```
meta {
  name: Create user
  type: http
}

post {
  url: {{baseUrl}}/users
  body: json
}

headers {
  Authorization: Bearer {{token}}
}

body:json {
  {
    "name": "김철수",
    "email": "kim@example.com"
  }
}
```

## 환경 변수

로컬·스테이징·운영을 오갈 때 URL과 토큰만 바뀝니다. 환경 파일로 분리합니다.

```
vars {
  baseUrl: https://api.example.com
}
vars:secret [
  token
]
```

`vars:secret`에 넣은 값은 파일에 저장되지 않으므로 토큰이 커밋되지 않습니다.
운영 토큰은 절대 일반 변수에 넣지 마세요.

## 테스트 붙이기

응답을 검증하는 스크립트를 요청마다 넣을 수 있습니다.

```javascript
test("201을 반환한다", function() {
  expect(res.getStatus()).to.equal(201);
});

test("id가 있다", function() {
  expect(res.getBody().id).to.be.a("string");
});
```

CLI로 전체 컬렉션을 실행하면 CI에서도 돌릴 수 있습니다.

```bash
npm install -g @usebruno/cli
bru run --env local
```

Postman은 같은 역할을 `newman`이 합니다.

```bash
npm install -g newman
newman run collection.json -e local-env.json
```

## 마이그레이션

Postman 컬렉션을 내보낸 뒤 Bruno에서 가져올 수 있습니다. curl 명령을 붙여넣어
요청으로 변환하는 기능도 양쪽 모두 지원합니다.

## 다음 단계

로컬에서 만든 API를 외부에 잠깐 노출해야 한다면 →
[로컬 서버 외부 노출](/docs/network/tunneling/)
