---
weight: 3020
title: "fd — 파일 찾기"
description: "find의 복잡한 문법 없이 파일명으로 빠르게 찾는 도구."
icon: "folder_open"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

`find . -name "*.log" -type f -not -path "*/node_modules/*"` 를 기억하지 못해도
됩니다. `fd log` 한 줄이면 대부분 끝납니다.

## 설치

```bash
brew install fd
sudo apt install fd-find        # 실행 파일 이름이 fdfind
winget install sharkdp.fd
```

Ubuntu에서는 별칭을 걸어둡니다.

```bash
alias fd=fdfind
```

## 기본 사용

```bash
fd config                # 이름에 config가 들어간 것 전부
fd -e md                 # 확장자가 md인 파일
fd -e md -e mdx docs/    # 여러 확장자, 특정 폴더에서
fd -H secret             # 숨김 파일 포함
fd -t d node_modules     # 디렉터리만
```

## 자주 쓰는 옵션

| 옵션 | 의미 |
|---|---|
| `-e <확장자>` | 확장자 필터 |
| `-t f` / `-t d` / `-t l` | 파일 / 디렉터리 / 심볼릭 링크 |
| `-H` | 숨김 파일 포함 |
| `-I` | `.gitignore` 무시 |
| `-d 2` | 탐색 깊이 제한 |
| `-s` | 대소문자 구분 |
| `--changed-within 1d` | 하루 안에 바뀐 것 |
| `-x <명령>` | 결과 각각에 명령 실행 |
| `-X <명령>` | 결과 전체를 한 번에 인자로 전달 |

## 찾은 다음 바로 실행

```bash
# 모든 png를 webp로 변환
fd -e png -x cwebp {} -o {.}.webp

# 30일 넘은 로그 삭제 (먼저 목록으로 확인!)
fd -e log --changed-before 30d
fd -e log --changed-before 30d -X rm

# 모든 package.json 위치 확인
fd -H '^package\.json$' -t f
```

`{}`는 파일 경로, `{.}`는 확장자를 뺀 경로, `{/}`는 파일명만을 뜻합니다.

## find와 비교

| 하고 싶은 일 | find | fd |
|---|---|---|
| 이름으로 찾기 | `find . -name "*conf*"` | `fd conf` |
| 확장자로 | `find . -name "*.md"` | `fd -e md` |
| 디렉터리만 | `find . -type d -name dist` | `fd -t d dist` |
| 최근 수정 | `find . -mtime -1` | `fd --changed-within 1d` |

기본적으로 `.gitignore`를 존중하기 때문에, 프로젝트 폴더에서 결과가 깨끗합니다.
반대로 빌드 산출물까지 봐야 한다면 `-I`를 붙입니다.

## 다음 단계

파일을 찾았으면 읽을 차례입니다 → [bat — 파일 읽기](/docs/files/bat/)
