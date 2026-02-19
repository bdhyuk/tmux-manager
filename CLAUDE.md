# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

tmux-manager는 tmux + fzf 기반의 터미널 세션 매니저로, 단일 bash 스크립트(`tmux-manager`)로 구성된다. 빌드/컴파일 과정 없이 직접 실행되는 bash 스크립트이며, 별도의 테스트/린트 시스템은 없다.

## Running

```bash
# 초기 설정 (tmux.conf에 권장 설정 추가)
./tmux-manager --setup

# 매니저 실행
./tmux-manager

# 설치 (PATH에 추가)
cp tmux-manager ~/.local/bin/ && chmod +x ~/.local/bin/tmux-manager
```

의존성: bash 4.0+, tmux 3.1+, fzf 0.36+, curl

## Architecture

단일 파일 `tmux-manager`가 모든 기능을 포함한다. `case "${1:-}"` 기반으로 서브커맨드를 분기하며, fzf의 `--bind execute()`를 통해 자기 자신을 재호출하는 구조.

### 실행 흐름

1. **Main entry (line ~343)**: `_tmux_mgr` 세션 생성 → 좌(30%)/우(70%) pane 분할 → 좌측에서 `--run-selector` 실행
2. **`--run-selector`**: fzf 실행 + 백그라운드 자동 갱신 루프(1초 간격, fzf `--listen` 포트로 `reload-sync` 전송)
3. **fzf 키 바인딩**: enter/ctrl-n/d/r/s/u/h → 각각 `--connect`, `--action-new/delete/rename/send`, `--update`, `--action-help` 호출

### 핵심 서브커맨드

| 서브커맨드 | 역할 |
|---|---|
| `--setup` | `~/.tmux.conf`에 설정 추가 (멱등) |
| `--list` | 세션 목록 출력, 디렉토리별 그룹화, Claude Code 상태 감지 |
| `--preview` | fzf 프리뷰 창용 세션 상세 정보 |
| `--connect` | 우측 pane에 세션 attach |
| `--run-selector` | fzf 메인 UI + 자동 갱신 루프 |
| `--action-*` | 세션 생성/삭제/이름변경/명령전송 |
| `--update` | GitHub에서 새 버전 다운로드 |

### 주요 설계 결정

- **`§` (section sign)를 필드 구분자로 사용** — 탭이나 공백이 세션 이름/경로에 포함될 수 있어 안전한 구분자 선택
- **`pane_title`로 Claude Code 상태 감지** — `✳` 접두사 = idle, 그 외 = working(spinner), idle 상태에서 pane 하단 스캔으로 WAIT 감지
- **매니저 prefix를 `Ctrl+a`로 분리** — 내부 세션의 `Ctrl+b`가 그대로 통과되도록
- **`env -u TMUX`로 중첩 tmux 해결** — 매니저 세션 내에서 다른 세션에 attach할 때 TMUX 환경변수 제거
- **fzf `--listen` 포트로 외부 갱신** — curl POST로 fzf 목록을 주기적으로 reload

## Conventions

- 커밋 메시지: Conventional Commits (`feat:`, `fix:`, `perf:`, `docs:`)
- 버전: `VERSION` 파일과 스크립트 내 `VERSION` 변수를 동기화해야 함
- 한국어 문서, 영어 코드 주석
- `set -uo pipefail` 사용 (bash strict mode, `-e` 제외)
