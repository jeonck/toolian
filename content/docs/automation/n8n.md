---
weight: 9050
title: "n8n"
description: "서비스와 API를 노드로 연결해 워크플로를 만드는 셀프호스팅 자동화 도구."
icon: "account_tree"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

"GitHub 이슈가 열리면 슬랙에 알리고 노션에 행을 추가한다" 같은 연결 작업을 코드
없이 만드는 도구입니다. Zapier·Make와 비슷하지만 **직접 호스팅할 수 있어 데이터가
외부로 나가지 않는다**는 점이 다릅니다.

## Docker로 실행

```bash
docker run -d --name n8n \
  -p 5678:5678 \
  -v n8n_data:/home/node/.n8n \
  -e GENERIC_TIMEZONE="Asia/Seoul" \
  -e TZ="Asia/Seoul" \
  docker.n8n.io/n8nio/n8n
```

`http://localhost:5678`에서 계정을 만들고 시작합니다.

Compose로 관리한다면:

```yaml
services:
  n8n:
    image: docker.n8n.io/n8nio/n8n
    ports:
      - "5678:5678"
    environment:
      - GENERIC_TIMEZONE=Asia/Seoul
      - N8N_HOST=n8n.example.com
      - WEBHOOK_URL=https://n8n.example.com/
    volumes:
      - n8n_data:/home/node/.n8n
    restart: unless-stopped

volumes:
  n8n_data:
```

## 개념

| 요소 | 설명 |
|---|---|
| **워크플로** | 노드를 연결한 하나의 자동화 |
| **트리거 노드** | 시작점. 스케줄, 웹훅, 앱 이벤트 |
| **일반 노드** | 앱 호출, 변환, 조건 분기 |
| **실행(execution)** | 워크플로가 한 번 돈 기록. 각 단계 데이터를 볼 수 있음 |

## 만들어볼 만한 워크플로

1. **에러 알림 정리**: 웹훅으로 에러를 받아 → 중복 제거 → 심각도별로 슬랙 채널
   분기
2. **일일 리포트**: 매일 아침 9시 트리거 → DB 조회 → 표로 가공 → 이메일 발송
3. **이슈 자동 분류**: GitHub 이슈 생성 → 본문을 LLM 노드로 분류 → 라벨 부여
4. **백업 확인**: S3 버킷 조회 → 어제 파일이 없으면 알림

## 코드가 필요한 부분

Code 노드에서 JavaScript로 데이터를 가공할 수 있습니다.

```javascript
// 입력 항목들을 정리해서 반환
return items.map(item => ({
  json: {
    title: item.json.title.trim(),
    url: item.json.html_url,
    createdAt: new Date(item.json.created_at).toISOString(),
  }
}));
```

## 셀프호스팅 시 챙길 것

- **HTTPS**: 웹훅을 받으려면 공개 주소가 필요합니다. 리버스 프록시(Caddy, Nginx)
  뒤에 두거나 Cloudflare Tunnel을 씁니다.
- **인증**: 기본 인증이나 SSO를 반드시 켭니다. 워크플로에는 각종 API 키가
  들어 있습니다.
- **백업**: `n8n_data` 볼륨에 워크플로와 자격증명이 저장됩니다. 정기 백업 대상입니다.
- **버전 업데이트**: 노드 동작이 바뀔 수 있으므로 업데이트 후 주요 워크플로를
  한 번씩 실행해 확인합니다.

## 언제 쓰고 언제 코드로 쓰나

| 상황 | 선택 |
|---|---|
| 서비스 3~4개를 단순 연결 | n8n |
| 조건 분기가 복잡하고 테스트가 필요 | 코드 + CI |
| 비개발자도 수정해야 함 | n8n |
| 초당 수백 건 처리 | 코드 (전용 워커) |

## 다음 단계

자동화한 내용을 기록으로 남기려면 → [문서 & 노트](/docs/writing/)
