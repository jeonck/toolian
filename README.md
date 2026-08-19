# Toolian

IT 생산성과 편의를 높여주는 도구를 소개하고 간단한 사용법을 제공하는 지식베이스.

**https://toolian.metacog.co.kr**

## 구성

- Hugo (extended) + [Lotus Docs](https://github.com/colinwilson/lotusdocs) (Hugo Module)
- GitHub Actions로 빌드해 GitHub Pages에 배포
- 10개 카테고리, 44개 문서

| 카테고리 | 내용 |
|---|---|
| 시작하기 | 도구 선택 기준, 패키지 매니저, dotfiles |
| 터미널 & 셸 | 터미널 앱, zsh/Starship, tmux, fzf, zoxide |
| 파일 & 검색 | ripgrep, fd, bat, eza, jq |
| 에디터 & IDE | VS Code, Neovim, JetBrains, 공통 편집 기술 |
| AI 코딩 도구 | Claude Code, Copilot, Cursor, Ollama |
| Git & 협업 | Git 워크플로, gh, lazygit, delta, pre-commit |
| 네트워크 & API | curl, HTTPie, Bruno/Postman, 터널링 |
| 컨테이너 & 배포 | Docker, Compose, kubectl/k9s, Actions, Terraform |
| 자동화 & 단축키 | Raycast, PowerToys, Make, cron/launchd, n8n |
| 문서 & 노트 | Obsidian, Markdown, Mermaid, Excalidraw |

## 로컬 실행

```bash
hugo server
```

Hugo Extended와 Go 툴체인이 필요합니다 (Lotus Docs가 Hugo Module로 배포되므로
모듈 해석에 Go가 필요합니다).

## 문서 추가하기

`content/docs/<카테고리>/<문서>.md` 로 추가합니다. 프론트매터의 `weight`는
**사이트 전체에서 유일해야 하며**, 다음 공식을 따릅니다.

```
weight = 1000 × (카테고리 순번) + 10 × (카테고리 내 문서 순번)
```

Lotus Docs의 이전/다음 네비게이션은 `/docs/` 아래 모든 페이지를 weight로 정렬해
계산하기 때문에, 카테고리마다 10/20/30을 반복하면 이전·다음 카드가 엉뚱한
카테고리로 튑니다.

## 배포

`main` 브랜치에 푸시하면 `.github/workflows/hugo.yml`이 빌드해 Pages에 배포합니다.
커스텀 도메인은 `static/CNAME`에 기록되어 있으며, Hugo가 매 빌드마다
`public/CNAME`으로 복사합니다.
