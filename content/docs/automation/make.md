---
weight: 9030
title: "Make와 Makefile"
description: "프로젝트의 모든 명령을 한 파일에 모아 `make test` 한 줄로 통일하기."
icon: "play_arrow"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

README에 적힌 긴 명령을 매번 복사해 붙여넣고 있다면 Makefile 하나로 정리할 수
있습니다. Make는 어느 유닉스 계열에나 있고, 언어를 가리지 않습니다.

## 최소 Makefile

프로젝트 루트에 `Makefile` (탭 들여쓰기 필수):

```makefile
.PHONY: help install dev test lint build clean

help:  ## 사용 가능한 명령 보기
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | \
	  awk 'BEGIN {FS = ":.*?## "}; {printf "  \033[36m%-12s\033[0m %s\n", $$1, $$2}'

install:  ## 의존성 설치
	npm ci

dev:  ## 개발 서버 실행
	docker compose up -d
	npm run dev

test:  ## 테스트 실행
	npm test

lint:  ## 린트와 포맷 검사
	npm run lint
	npm run format:check

build:  ## 프로덕션 빌드
	npm run build

clean:  ## 산출물 정리
	rm -rf dist node_modules/.cache
	docker compose down
```

`make` 또는 `make help`를 치면 명령 목록이 설명과 함께 나옵니다. 새 팀원이
README를 안 읽어도 무엇을 할 수 있는지 보입니다.

## 알아둘 문법

| 문법 | 의미 |
|---|---|
| `.PHONY: 이름` | 같은 이름의 파일이 있어도 항상 실행 |
| `target: 의존` | 의존 타깃을 먼저 실행 |
| `@명령` | 명령 자체를 출력하지 않음 |
| `-명령` | 실패해도 계속 진행 |
| `$$` | 셸의 `$`를 쓸 때 (Make의 `$`와 구분) |
| `VAR ?= 값` | 이미 정의됐으면 유지 |

**들여쓰기는 반드시 탭입니다.** 스페이스를 쓰면
`Makefile:5: *** missing separator` 에러가 납니다. 에디터에서 Makefile은
탭을 유지하도록 설정하세요 (`.editorconfig`의 `[Makefile] indent_style = tab`).

## 변수와 인자

```makefile
ENV ?= dev
IMAGE = myapp:$(ENV)

build:
	docker build -t $(IMAGE) .

deploy:
	./deploy.sh $(ENV)
```

```bash
make deploy ENV=prod
```

## 각 줄은 별도 셸입니다

```makefile
# 잘못됨 — cd가 다음 줄에 유지되지 않음
wrong:
	cd src
	npm test

# 올바름
right:
	cd src && npm test
```

## 대안

| 도구 | 특징 |
|---|---|
| **just** | Make 문법의 함정(탭, 별도 셸)이 없음. `justfile` 사용 |
| **npm scripts** | Node 프로젝트면 이미 있음. 복잡해지면 한계 |
| **Taskfile** | YAML 기반, 의존성·조건 지원 |

```bash
brew install just
just --list
```

Make의 가장 큰 장점은 **어디에나 이미 있다는 것**입니다. 새 도구 설치를 요구하지
않는 것이 중요하다면 Make, 문법 편의가 우선이면 just를 고릅니다.

## 다음 단계

정해진 시각에 도는 작업이 필요하다면 →
[cron과 launchd](/docs/automation/schedulers/)
