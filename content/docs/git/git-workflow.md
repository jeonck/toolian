---
weight: 6010
title: "매일 쓰는 Git 워크플로"
description: "브랜치·커밋·리베이스의 실전 흐름과, 실수를 되돌리는 명령 모음."
icon: "account_tree"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Git 명령은 수백 개지만 실제로 매일 쓰는 것은 열 개 남짓입니다. 여기서는 그 열
개와, 사고가 났을 때 되돌리는 방법을 정리합니다.

## 하루의 흐름

```bash
git switch main && git pull --rebase     # 최신 상태로
git switch -c feat/login-rate-limit      # 작업 브랜치
# ... 작업 ...
git add -p                               # 변경을 조각 단위로 검토하며 스테이징
git commit -m "로그인 요청에 분당 5회 제한 추가"
git push -u origin feat/login-rate-limit
```

`git add -p`는 변경 사항을 조각(hunk)마다 보여주며 포함 여부를 묻습니다. 디버깅용
`print`가 섞여 들어가는 사고를 대부분 막아줍니다.

## 자주 쓰는 명령

| 목적 | 명령 |
|---|---|
| 상태 간략히 | `git status -sb` |
| 변경 보기 | `git diff` / 스테이징된 것은 `git diff --staged` |
| 로그 한 줄씩 | `git log --oneline --graph --decorate -20` |
| 브랜치 전환 | `git switch <브랜치>` |
| 새 브랜치 | `git switch -c <브랜치>` |
| 잠시 치워두기 | `git stash` / 되돌리기 `git stash pop` |
| 특정 커밋만 가져오기 | `git cherry-pick <해시>` |
| 원격 브랜치 정리 | `git fetch --prune` |

## 커밋 메시지

형식을 정해두면 나중에 히스토리를 읽기가 훨씬 쉽습니다. 널리 쓰이는 규칙:

```
feat: 로그인 요청에 분당 5회 제한 추가
fix: 만료 토큰이 401 대신 500을 반환하던 문제 수정
refactor: 인증 미들웨어를 auth/ 로 분리
docs: 배포 절차에 롤백 단계 추가
test: 토큰 갱신 실패 케이스 추가
chore: 의존성 업데이트
```

제목은 50자 안쪽, 명령형으로. 본문이 필요하면 한 줄 띄우고 **왜** 바꿨는지 적습니다.
무엇을 바꿨는지는 diff가 이미 말해줍니다.

## 실수를 되돌리기

| 상황 | 명령 |
|---|---|
| 방금 커밋 메시지 고치기 | `git commit --amend` |
| 방금 커밋 취소 (변경은 유지) | `git reset --soft HEAD~1` |
| 스테이징만 취소 | `git restore --staged <파일>` |
| 파일 변경 통째로 버리기 | `git restore <파일>` |
| 이미 푸시한 커밋 되돌리기 | `git revert <해시>` |
| 브랜치를 통째로 되감기 | `git reset --hard <해시>` (주의) |
| 지운 커밋 찾기 | `git reflog` → 해시 확인 후 `git reset --hard <해시>` |

`git reflog`는 최후의 안전망입니다. `reset --hard`로 날린 커밋도 대부분 여기서
찾을 수 있습니다(기본 90일 보관).

**푸시한 커밋은 `revert`, 로컬 커밋만 `reset`** 이 원칙입니다. 공유된 히스토리를
바꾸면 다른 사람의 저장소가 깨집니다.

## 리베이스와 머지

```bash
# 내 브랜치를 최신 main 위로 옮기기 (히스토리가 선형이 됨)
git switch feat/login
git fetch origin
git rebase origin/main

# 충돌이 나면 파일 수정 후
git add <파일>
git rebase --continue

# 포기하려면
git rebase --abort
```

커밋을 정리해서 올리고 싶다면 대화형 리베이스를 씁니다.

```bash
git rebase -i origin/main
# pick → squash(s) 로 바꾸면 앞 커밋에 합쳐짐
```

## 설정 추천

```bash
git config --global pull.rebase true          # pull 시 머지 커밋 안 만들기
git config --global push.autoSetupRemote true # push 시 -u 생략 가능
git config --global init.defaultBranch main
git config --global rerere.enabled true       # 반복 충돌 해결 기억
git config --global fetch.prune true
```

## 다음 단계

브라우저를 오가지 않고 PR과 이슈를 다루려면 →
[GitHub CLI (gh)](/docs/git/github-cli/)
