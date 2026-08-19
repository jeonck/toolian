---
weight: 6050
title: "pre-commit 훅"
description: "커밋 시점에 포맷·린트·비밀키 검사를 자동 실행해 리뷰에서 지적을 없애기."
icon: "verified"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

"포맷이 안 맞습니다", "여기 콘솔 로그가 남았네요" 같은 리뷰 코멘트는 사람이
아니라 기계가 잡아야 할 것들입니다. pre-commit은 커밋할 때마다 정해진 검사를
자동으로 돌려줍니다.

## 설치

```bash
brew install pre-commit
pip install pre-commit
```

프로젝트 루트에서 훅을 설치합니다.

```bash
pre-commit install
```

이제 `git commit`할 때마다 자동 실행됩니다.

## 설정 파일

프로젝트 루트에 `.pre-commit-config.yaml`:

```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.6.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-json
      - id: check-merge-conflict
      - id: check-added-large-files
        args: ["--maxkb=1000"]

  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.6.9
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format

  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.19.2
    hooks:
      - id: gitleaks
```

`gitleaks`는 API 키나 토큰이 커밋에 섞여 들어가는 것을 막아줍니다. 한 번 푸시된
비밀키는 회수가 사실상 불가능하므로, 이 훅 하나만으로도 도입 가치가 있습니다.

## 자주 쓰는 명령

| 명령 | 동작 |
|---|---|
| `pre-commit run` | 스테이징된 파일에 대해 실행 |
| `pre-commit run --all-files` | 저장소 전체에 실행 (도입 직후 한 번) |
| `pre-commit autoupdate` | 훅 버전을 최신으로 |
| `pre-commit run <hook-id>` | 특정 훅만 |
| `git commit --no-verify` | 훅 건너뛰기 (긴급 시에만) |

## 도입 순서

1. 먼저 `--all-files`로 전체를 한 번 정리하고 **그 결과만 담은 커밋**을 따로 만듭니다.
   기능 변경과 포맷 변경이 섞이면 리뷰가 불가능해집니다.
2. 그 커밋 해시를 `.git-blame-ignore-revs`에 넣어 blame이 오염되지 않게 합니다.
   ```bash
   echo "<포맷-커밋-해시>" >> .git-blame-ignore-revs
   git config blame.ignoreRevsFile .git-blame-ignore-revs
   ```
3. 팀에 `pre-commit install`을 한 번 실행하도록 안내합니다. 훅은 저장소에 자동
   적용되지 않고 각자 설치해야 합니다.
4. CI에서도 같은 검사를 돌려, 훅을 설치하지 않은 사람의 커밋도 걸러냅니다.

```yaml
# .github/workflows/lint.yml 의 일부
- uses: pre-commit/action@v3.0.1
```

## 느려질 때

- 무거운 훅(타입 체크, 테스트)은 pre-commit이 아니라 pre-push나 CI로 옮깁니다.
  ```bash
  pre-commit install --hook-type pre-push
  ```
- `files:` / `exclude:` 로 대상 범위를 좁힙니다.

## 다음 단계

코드가 정리됐다면 이제 외부와 통신하는 도구로 넘어갑니다 →
[네트워크 & API](/docs/network/)
