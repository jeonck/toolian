---
weight: 5020
title: "GitHub Copilot"
description: "에디터 안에서 다음 줄을 제안하는 인라인 자동완성. 반복 코드에서 효과가 크다."
icon: "auto_fix_high"
date: "2026-08-19"
lastmod: "2026-08-19"
draft: false
---

Copilot은 지금 커서 위치에서 이어질 코드를 회색으로 제안합니다. 에이전트처럼
프로젝트를 돌아다니지는 않지만, **타이핑을 줄이는 빈도**로는 가장 자주 도움이 되는
방식입니다.

## 설치

VS Code 확장 마켓플레이스에서 `GitHub Copilot`을 설치하고 GitHub 계정으로
로그인합니다. JetBrains, Neovim, Visual Studio용 플러그인도 제공됩니다.

```bash
# Neovim
git clone https://github.com/github/copilot.vim \
  ~/.config/nvim/pack/github/start/copilot.vim
# 실행 후 :Copilot setup
```

## 기본 조작

| 동작 | 키 |
|---|---|
| 제안 수락 | `Tab` |
| 제안 거절 | `Esc` |
| 다음/이전 제안 | `⌥]` / `⌥[` (Alt) |
| 제안 여러 개 패널로 보기 | `⌃⏎` (Ctrl+Enter) |
| 인라인 채팅 | `⌘I` / `Ctrl+I` |

## 제안을 잘 받아내는 법

Copilot은 **주변 문맥**을 보고 예측합니다. 문맥을 명확하게 만들수록 정확해집니다.

1. **주석으로 의도를 먼저 씁니다.**
   ```python
   # CSV 파일을 읽어 날짜 컬럼을 파싱하고, 결측치가 있는 행은 제외한다
   def load_sales(path: str) -> pd.DataFrame:
   ```
2. **함수 시그니처와 타입을 먼저 씁니다.** 반환 타입이 있으면 구현 정확도가 올라갑니다.
3. **관련 파일을 열어둡니다.** 열려 있는 탭이 문맥으로 쓰입니다.
4. **이름을 정확하게 짓습니다.** `handleData`보다 `parseInvoiceRow`가 더 나은 제안을 부릅니다.

## 특히 효과가 큰 경우

- 테스트 케이스 작성 (한 개를 쓰면 나머지 패턴을 이어감)
- 반복적인 매핑·변환 코드
- 정규식, 날짜 포맷 문자열처럼 기억하기 힘든 것
- 문서 주석(JSDoc, docstring) 생성

## 주의할 점

- **그럴듯한 오답이 나옵니다.** 특히 API 시그니처를 지어내는 경우가 있으니, 낯선
  라이브러리라면 문서로 확인하세요.
- **보안 코드에 그대로 쓰지 않습니다.** 인증·암호화·권한 검사 코드는 반드시 직접
  검토합니다.
- **비공개 코드 정책을 확인하세요.** 조직 정책상 제안 필터링이나 사용 제한이 있을
  수 있습니다.

## Claude Code와 함께 쓰기

두 도구는 겹치지 않습니다.

| 상황 | 도구 |
|---|---|
| 지금 이 줄을 빨리 끝내고 싶다 | Copilot |
| 여러 파일을 고쳐야 한다 | Claude Code |
| 코드를 이해하고 싶다 | 둘 다 (채팅으로 질문) |

## 다음 단계

에디터 자체가 AI를 중심으로 설계된 쪽을 보려면 → [Cursor](/docs/ai/cursor/)
