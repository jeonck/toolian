---
weight: 8010
title: "Docker"
description: "환경을 코드로 고정하는 컨테이너 기본기 — 이미지, 컨테이너, Dockerfile."
icon: "inventory"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Docker의 효용은 "가상화"보다 **환경을 코드로 적어두는 것**에 있습니다. 새 팀원이
반나절 걸리던 로컬 환경 세팅이 `docker compose up` 한 줄이 됩니다.

## 설치

```bash
brew install --cask docker          # Docker Desktop
brew install --cask orbstack        # 더 가벼운 대안 (macOS)
```

Linux는 배포판 공식 문서의 `docker-ce` 설치 절차를 따릅니다.

## 개념 세 가지

| 개념 | 설명 |
|---|---|
| **이미지** | 실행 환경의 스냅샷. 읽기 전용 |
| **컨테이너** | 이미지를 실행한 인스턴스 |
| **볼륨** | 컨테이너가 죽어도 남는 저장 공간 |

## 기본 명령

```bash
docker run -d --name pg -p 5432:5432 \
  -e POSTGRES_PASSWORD=secret postgres:16

docker ps                    # 실행 중 컨테이너
docker ps -a                 # 종료된 것 포함
docker logs -f pg            # 로그 실시간
docker exec -it pg psql -U postgres    # 컨테이너 안에서 명령 실행
docker stop pg && docker rm pg
```

| 옵션 | 의미 |
|---|---|
| `-d` | 백그라운드 실행 |
| `-p 호스트:컨테이너` | 포트 연결 |
| `-e KEY=VALUE` | 환경 변수 |
| `-v 호스트경로:컨테이너경로` | 볼륨 마운트 |
| `--rm` | 종료 시 자동 삭제 |
| `-it` | 대화형 터미널 |

## Dockerfile 작성

```dockerfile
# 1단계: 빌드
FROM node:22-slim AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# 2단계: 실행 (빌드 도구 없이 가볍게)
FROM node:22-slim
WORKDIR /app
ENV NODE_ENV=production
COPY package*.json ./
RUN npm ci --omit=dev
COPY --from=builder /app/dist ./dist
USER node
EXPOSE 3000
CMD ["node", "dist/main.js"]
```

핵심은 두 가지입니다.

- **레이어 캐시**: 자주 바뀌지 않는 것(의존성)을 먼저, 자주 바뀌는 것(소스)을
  나중에 복사합니다. 순서만 바꿔도 빌드가 몇 배 빨라집니다.
- **멀티 스테이지**: 빌드 도구는 최종 이미지에 넣지 않습니다. 이미지 크기와
  공격 표면이 함께 줄어듭니다.

`.dockerignore`도 잊지 마세요.

```
node_modules
.git
dist
*.log
.env
```

## 빌드와 실행

```bash
docker build -t myapp:dev .
docker run --rm -p 3000:3000 --env-file .env myapp:dev
```

## 정리

컨테이너와 이미지는 조용히 디스크를 잡아먹습니다.

```bash
docker system df           # 사용량 확인
docker container prune     # 멈춘 컨테이너 삭제
docker image prune -a      # 사용하지 않는 이미지 삭제
docker system prune -a --volumes    # 전부 (볼륨 포함, 주의)
```

## 자주 겪는 문제

| 증상 | 원인과 해결 |
|---|---|
| 포트 충돌 | 이미 그 포트를 쓰는 프로세스. `lsof -i :5432`로 확인 |
| 파일 변경이 반영 안 됨 | 소스를 이미지에 복사만 함. 개발 시엔 `-v $(pwd):/app` 마운트 |
| Apple Silicon에서 실행 실패 | `--platform linux/amd64` 지정 |
| 컨테이너가 바로 종료됨 | 포그라운드 프로세스가 없음. `docker logs`로 확인 |

## 다음 단계

여러 서비스를 한 번에 띄우려면 → [Docker Compose](/docs/devops/docker-compose/)
