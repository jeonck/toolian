---
weight: 6020
title: "GitHub CLI (gh)"
description: "PR 생성·리뷰·머지와 이슈 관리, Actions 확인을 터미널에서 끝내기."
icon: "terminal"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

`gh`는 GitHub 웹에서 하던 일 대부분을 터미널로 옮겨줍니다. 브라우저를 열고 탭을
찾고 버튼을 누르는 흐름이 사라지는 것이 가장 큰 이득입니다.

## 설치와 로그인

```bash
brew install gh
sudo apt install gh
winget install GitHub.cli

gh auth login          # 브라우저 인증
gh auth status         # 현재 로그인 확인
```

## Pull Request

```bash
gh pr create --fill                  # 커밋 메시지로 제목·본문 자동 작성
gh pr create --draft --title "WIP: 인증 리팩터링"

gh pr list                           # 열린 PR 목록
gh pr list --author "@me"            # 내 PR만
gh pr status                         # 나와 관련된 PR 요약

gh pr checkout 42                    # PR 브랜치로 전환 (리뷰용)
gh pr diff 42                        # 변경 내용 보기
gh pr review 42 --approve
gh pr review 42 --request-changes -b "테스트 추가 부탁드립니다"
gh pr merge 42 --squash --delete-branch
```

리뷰하러 갈 때 `gh pr checkout`은 특히 유용합니다. 원격 브랜치 이름을 몰라도
번호만으로 바로 받아옵니다.

## 이슈

```bash
gh issue create --title "로그인 후 리다이렉트 실패" --body "재현 절차..."
gh issue list --label bug --state open
gh issue view 17 --comments
gh issue close 17
```

## Actions 확인

```bash
gh run list --limit 5          # 최근 실행 목록
gh run watch                   # 진행 중인 실행을 실시간으로
gh run view <run-id> --log-failed   # 실패한 스텝의 로그만
gh workflow run deploy.yml     # 수동 트리거
```

푸시하고 브라우저에서 새로고침을 반복하는 대신 `gh run watch` 하나로 끝납니다.

## 저장소 관리

```bash
gh repo create myorg/newrepo --private --source=. --remote=origin --push
gh repo clone jeonck/toolian
gh repo view --web            # 브라우저에서 열기
gh release create v1.2.0 --generate-notes
```

## API 직접 호출

CLI가 감싸지 않은 기능은 `gh api`로 부릅니다. 인증이 자동으로 붙습니다.

```bash
gh api repos/jeonck/toolian --jq '.stargazers_count'
gh api -X PUT repos/jeonck/toolian/pages -f cname=toolian.metacog.co.kr
```

불리언 값을 보낼 때는 소문자 `-f`가 아니라 대문자 `-F`를 써야 문자열이 아닌 진짜
JSON 불리언으로 전송됩니다.

```bash
gh api -X PUT repos/OWNER/REPO/pages -F https_enforced=true
```

## 별칭

```bash
gh alias set prs 'pr list --author "@me"'
gh alias set co 'pr checkout'
gh prs
gh co 42
```

## 다음 단계

스테이징과 히스토리를 눈으로 보며 다루고 싶다면 → [lazygit](/docs/git/lazygit/)
