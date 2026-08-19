---
weight: 8020
title: "Docker Compose"
description: "앱·DB·캐시를 파일 하나로 정의하고 한 명령으로 띄우는 로컬 개발 환경."
icon: "layers"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

실제 프로젝트는 컨테이너 하나로 끝나지 않습니다. 앱, 데이터베이스, 캐시, 큐가
함께 떠야 합니다. Compose는 이 구성을 YAML 하나에 적고 `docker compose up`으로
띄웁니다.

## compose.yaml

```yaml
services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      DATABASE_URL: postgres://postgres:secret@db:5432/app
      REDIS_URL: redis://cache:6379
    volumes:
      - .:/app
      - /app/node_modules      # 호스트 폴더로 덮이지 않게 보호
    depends_on:
      db:
        condition: service_healthy

  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: app
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      retries: 5

  cache:
    image: redis:7-alpine

volumes:
  pgdata:
```

컨테이너끼리는 **서비스 이름으로 서로를 찾습니다**. 위에서 앱이 DB에 접속할 때
`localhost`가 아니라 `db`를 쓰는 이유입니다.

## 기본 명령

| 명령 | 동작 |
|---|---|
| `docker compose up -d` | 전체 백그라운드 실행 |
| `docker compose up --build` | 이미지 다시 빌드하고 실행 |
| `docker compose ps` | 상태 확인 |
| `docker compose logs -f app` | 특정 서비스 로그 |
| `docker compose exec app sh` | 실행 중 컨테이너에 접속 |
| `docker compose run --rm app npm test` | 일회성 명령 실행 |
| `docker compose down` | 중지 및 정리 |
| `docker compose down -v` | 볼륨까지 삭제 (데이터 초기화) |

## 환경별 구성 분리

기본 파일과 오버라이드 파일로 나눕니다.

```bash
# compose.yaml (공통) + compose.override.yaml (로컬, 자동 적용)
docker compose up

# 운영용 구성으로
docker compose -f compose.yaml -f compose.prod.yaml up -d
```

`compose.override.yaml`은 파일명만 맞으면 자동으로 합쳐지므로, 로컬 전용 볼륨
마운트나 디버그 포트를 여기 둡니다.

## `.env` 파일

```bash
# .env
POSTGRES_PASSWORD=secret
APP_PORT=3000
```

```yaml
services:
  app:
    ports:
      - "${APP_PORT}:3000"
```

`.env`는 반드시 `.gitignore`에 넣고, `.env.example`을 대신 커밋해 필요한 키
목록을 공유합니다.

## 자주 겪는 문제

| 증상 | 해결 |
|---|---|
| 앱이 DB에 접속 못 함 | 호스트를 `localhost` 대신 서비스 이름으로 |
| DB가 아직 준비 안 됨 | `depends_on` + `healthcheck` 조합 사용 |
| 코드 변경이 반영 안 됨 | 소스 볼륨 마운트 확인, 핫 리로드 설정 확인 |
| `node_modules`가 사라짐 | 익명 볼륨(`/app/node_modules`)으로 보호 |
| 포트 이미 사용 중 | `.env`의 포트 값을 바꿔 회피 |

## 다음 단계

컨테이너가 여러 서버로 퍼지면 오케스트레이터가 필요합니다 →
[kubectl과 k9s](/docs/devops/kubernetes/)
