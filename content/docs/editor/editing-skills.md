---
weight: 4040
title: "에디터 공통 편집 기술"
description: "멀티 커서, 정규식 치환, LSP, EditorConfig — 도구를 바꿔도 남는 기술."
icon: "edit_note"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

에디터는 몇 년에 한 번 바뀌지만, 아래 기술들은 어디로 옮겨도 그대로 씁니다.
투자 대비 회수가 가장 확실한 영역입니다.

## 멀티 커서

같은 편집을 여러 줄에 반복할 때 씁니다.

| 동작 | VS Code | JetBrains |
|---|---|---|
| 같은 단어 다음 것 추가 | `⌘D` / `Ctrl+D` | `⌃G` / `Alt+J` |
| 모든 일치 항목 선택 | `⌘⇧L` / `Ctrl+Shift+L` | `⌃⌘G` / `Ctrl+Alt+Shift+J` |
| 위/아래로 커서 추가 | `⌥⌘↑↓` / `Ctrl+Alt+↑↓` | `⌥⌥↑↓` (Alt 두 번) |
| 임의 위치에 추가 | `⌥클릭` / `Alt+클릭` | 동일 |

전형적인 용도: 목록 열 개에 따옴표를 붙이거나, import 구문 여러 개를 동시에
고치는 작업.

## 정규식 찾아 바꾸기

멀티 커서로 감당이 안 되는 패턴은 정규식이 빠릅니다. 검색창의 `.*` 아이콘을 켜고
쓰며, `$1`, `$2`로 캡처 그룹을 참조합니다.

| 목적 | 찾기 | 바꾸기 |
|---|---|---|
| 함수 호출 인자 순서 바꾸기 | `move\((\w+), (\w+)\)` | `move($2, $1)` |
| `var`를 `const`로 (선언만) | `\bvar (\w+) =` | `const $1 =` |
| 콘솔 로그 제거 | `^\s*console\.log\(.*\);?\n` | (빈칸) |
| 따옴표 통일 | `'([^']*)'` | `"$1"` |

**주의**: 실행 전에 반드시 미리보기로 몇 건이 바뀌는지 확인하고, 커밋되지 않은
변경이 없는 상태에서 하세요. 되돌리기가 쉬워집니다.

## LSP를 이해하기

Language Server Protocol은 "언어 지식"을 에디터에서 분리한 규약입니다. 자동완성,
정의로 이동, 실시간 에러 표시는 전부 언어 서버가 제공하고, 에디터는 화면만
그립니다.

- VS Code는 확장이 언어 서버를 자동으로 설치합니다.
- Neovim은 `nvim-lspconfig` + `mason.nvim`으로 관리합니다.
- 같은 서버(`gopls`, `pyright`, `typescript-language-server`)를 여러 에디터가
  공유하므로, 에디터를 바꿔도 코드 인텔리전스 품질은 비슷하게 유지됩니다.

동작이 이상하면 언어 서버 로그부터 봅니다. 대개 프로젝트 루트를 잘못 잡았거나
의존성 설치가 안 된 것이 원인입니다.

## EditorConfig로 팀 규칙 고정

에디터가 제각각이어도 들여쓰기와 개행 규칙은 통일할 수 있습니다. 프로젝트 루트에
`.editorconfig`를 둡니다.

```ini
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
indent_style = space
indent_size = 2

[*.py]
indent_size = 4

[*.md]
trim_trailing_whitespace = false
```

대부분의 에디터가 기본 지원하거나 확장 하나로 지원합니다. 포맷 때문에 diff가
지저분해지는 문제를 근본적으로 줄여줍니다.

## 스니펫

같은 코드 블록을 반복해서 친다면 스니펫으로 등록합니다. VS Code는
`⌘⇧P → Snippets: Configure Snippets`:

```json
{
  "React 함수 컴포넌트": {
    "prefix": "rfc",
    "body": [
      "export function ${1:Name}() {",
      "  return <div>$0</div>;",
      "}"
    ]
  }
}
```

`$1`은 첫 커서, `$0`은 마지막 커서 위치입니다.

## 다음 단계

편집이 손에 붙었으면 AI 도구를 얹을 차례입니다 → [AI 코딩 도구](/docs/ai/)
