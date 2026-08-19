---
weight: 8040
title: "GitHub Actions"
description: "푸시할 때마다 테스트·빌드·배포를 자동으로 돌리는 CI/CD 구성."
icon: "play_circle"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Actions는 저장소 안에 워크플로 YAML을 두면 GitHub가 알아서 실행해 주는 CI/CD
서비스입니다. 별도 서버 없이 시작할 수 있어 개인 프로젝트에도 부담이 없습니다.

## 첫 워크플로

`.github/workflows/ci.yml`:

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: npm

      - run: npm ci
      - run: npm run lint
      - run: npm test
```

푸시하면 바로 실행됩니다. `gh run watch`로 터미널에서 진행 상황을 볼 수 있습니다.

## 핵심 개념

| 용어 | 의미 |
|---|---|
| **workflow** | YAML 파일 하나. 트리거와 잡을 정의 |
| **job** | 독립된 러너에서 실행되는 단위. 기본은 병렬 |
| **step** | 잡 안의 순차 실행 단위 |
| **action** | 재사용 가능한 스텝 (`uses:`) |
| **runner** | 실행 환경 (`ubuntu-latest` 등) |

## 자주 쓰는 패턴

**여러 버전에서 테스트 (매트릭스)**

```yaml
strategy:
  matrix:
    node: [20, 22]
    os: [ubuntu-latest, macos-latest]
runs-on: ${{ matrix.os }}
```

**의존성 캐시**

```yaml
- uses: actions/cache@v4
  with:
    path: ~/.cache/pip
    key: pip-${{ hashFiles('requirements.txt') }}
```

`setup-node`, `setup-python` 등은 `cache:` 옵션으로 더 간단히 처리됩니다.

**조건 실행**

```yaml
- name: 배포
  if: github.ref == 'refs/heads/main' && github.event_name == 'push'
  run: ./deploy.sh
```

**잡 간 의존성**

```yaml
jobs:
  build: { ... }
  deploy:
    needs: build
    runs-on: ubuntu-latest
```

## 시크릿

`Settings → Secrets and variables → Actions`에서 등록하고 이렇게 씁니다.

```yaml
- run: ./deploy.sh
  env:
    API_TOKEN: ${{ secrets.API_TOKEN }}
```

시크릿은 로그에서 자동으로 마스킹되지만, 스크립트가 값을 가공해 출력하면 새어
나갈 수 있습니다. `echo`로 찍지 마세요.

## 권한 최소화

기본 토큰 권한은 워크플로마다 명시하는 편이 안전합니다.

```yaml
permissions:
  contents: read
  pull-requests: write
```

## 디버깅

| 문제 | 확인 |
|---|---|
| 워크플로가 안 돌음 | 파일 위치(`.github/workflows/`), 브랜치, YAML 문법 |
| 로컬은 되는데 CI만 실패 | 러너에 없는 도구, 환경 변수, 파일 대소문자 |
| 캐시가 안 먹음 | key에 잠금 파일 해시가 들어갔는지 |
| 권한 오류 | `permissions:` 블록, 토큰 스코프 |

```bash
gh run list --limit 5
gh run view <id> --log-failed
```

로컬에서 워크플로를 실행해 보려면 [act](https://github.com/nektos/act)를 씁니다.

```bash
brew install act
act -j test
```

## 비용 주의

퍼블릭 저장소는 무료지만, 프라이빗 저장소는 실행 시간이 과금됩니다. macOS 러너는
Linux의 10배 단가이므로 꼭 필요할 때만 씁니다. `concurrency`로 중복 실행을
취소하면 낭비를 줄일 수 있습니다.

```yaml
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```

## 다음 단계

인프라 자체를 코드로 관리하려면 → [Terraform](/docs/devops/terraform/)
