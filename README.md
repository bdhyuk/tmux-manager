# tmux-manager

tmux 세션을 한 화면에서 관리하는 터미널 도구입니다.
왼쪽에서 세션을 선택하고, 오른쪽에서 바로 작업할 수 있습니다.

```
┌──────────────────┬─────────────────────────────────────────┐
│ session>         │                                         │
│ ▶ my-project  *  │  $ npm start                            │
│   webserver      │  > Server running on :3000              │
│   devtools       │  $                                      │
│                  │                                         │
│ ── Preview ───── │        (라이브 세션 - 직접 작업 가능)    │
│ Window 0: bash   │                                         │
│   Pane 0: 80x24  │                                         │
├──────────────────┴─────────────────────────────────────────┤
│ MGR                            ^A: mgr | ^B: session       │
└────────────────────────────────────────────────────────────┘
```

---

## 1. tmux 설치

### macOS

```bash
brew install tmux
```

> Homebrew가 없다면 먼저 설치하세요:
> ```bash
> /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
> ```

### Ubuntu / Debian

```bash
sudo apt update && sudo apt install -y tmux
```

### CentOS / Fedora

```bash
sudo dnf install -y tmux
```

### 설치 확인

```bash
tmux -V
# tmux 3.1 이상이어야 합니다
```

### tmux 기본 사용법

```bash
# 새 세션 만들기
tmux new-session -s my-session

# 세션에서 나가기 (detach)
# Ctrl+b 누른 후 d

# 세션 목록 보기
tmux list-sessions

# 세션 다시 접속
tmux attach -t my-session
```

---

## 2. fzf 설치

### macOS

```bash
brew install fzf
```

### Ubuntu / Debian

```bash
sudo apt install -y fzf
```

> apt 버전이 0.36 미만이면 직접 설치하세요:
> ```bash
> git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
> ~/.fzf/install --bin
> sudo cp ~/.fzf/bin/fzf /usr/local/bin/
> ```

### CentOS / Fedora

```bash
sudo dnf install -y fzf
```

### 설치 확인

```bash
fzf --version
# 0.36 이상이어야 합니다
```

---

## 3. curl 설치 (대부분 기본 설치됨)

```bash
# 확인
curl --version

# 없는 경우
sudo apt install -y curl    # Ubuntu/Debian
sudo dnf install -y curl    # CentOS/Fedora
brew install curl            # macOS (보통 이미 있음)
```

---

## 4. tmux-manager 설치

### 방법 A: 한 줄 설치

```bash
mkdir -p ~/.local/bin && \
curl -o ~/.local/bin/tmux-manager \
  https://raw.githubusercontent.com/<owner>/tmux-manager/main/tmux-manager && \
chmod +x ~/.local/bin/tmux-manager
```

### 방법 B: 직접 복사

```bash
cp tmux-manager ~/.local/bin/tmux-manager
chmod +x ~/.local/bin/tmux-manager
```

### 방법 C: 심볼릭 링크

```bash
ln -sf /path/to/tmux-manager ~/.local/bin/tmux-manager
```

### PATH 설정

`~/.local/bin`이 PATH에 없다면 셸 설정 파일에 추가하세요:

```bash
# ~/.zshrc 또는 ~/.bashrc에 추가
export PATH="$HOME/.local/bin:$PATH"
```

적용:

```bash
source ~/.zshrc   # 또는 source ~/.bashrc
```

---

## 5. 실행

```bash
tmux-manager
```

---

## 요구사항 요약

| 도구 | 최소 버전 | 확인 명령 | 용도 |
|------|-----------|-----------|------|
| **bash** | 4.0+ | `bash --version` | 스크립트 실행 |
| **tmux** | 3.1+ | `tmux -V` | 세션 관리 |
| **fzf** | 0.36+ | `fzf --version` | 인터랙티브 UI |
| **curl** | - | `curl --version` | 목록 자동 갱신 |

---

## 사용법

### 세션 목록 (왼쪽 패널)

| 키 | 동작 |
|----|------|
| `Enter` | 선택한 세션에 연결 (오른쪽 패널에 라이브 표시) |
| `Ctrl+n` | 새 세션 생성 |
| `Ctrl+d` | 세션 삭제 |
| `Ctrl+r` | 세션 이름 변경 |
| `Ctrl+s` | 세션에 명령어 전송 |
| `Ctrl+h` | tmux 단축키 치트시트 |
| `Esc` | 종료 |

### 패널 전환

| 키 | 동작 |
|----|------|
| `Ctrl+a ←` | 왼쪽 (세션 목록)으로 이동 |
| `Ctrl+a →` | 오른쪽 (라이브 세션)으로 이동 |
| 마우스 클릭 | 패널 직접 선택 |

### 패널 크기 조절

| 키 | 동작 |
|----|------|
| `Ctrl+a Ctrl+←` | 왼쪽으로 늘리기 |
| `Ctrl+a Ctrl+→` | 오른쪽으로 늘리기 |
| 마우스 드래그 | 경계선을 잡아서 조절 |

> **참고**: 매니저의 prefix는 `Ctrl+a`이고, 오른쪽 라이브 세션 안에서는 `Ctrl+b`가 그대로 동작합니다.

---

## 주요 기능

- **라이브 세션 연결** — 오른쪽 패널에서 세션에 직접 접속하여 작업
- **세션 관리** — 생성, 삭제, 이름 변경
- **자동 갱신** — 2초마다 세션 목록 자동 업데이트
- **Pane Title 표시** — Claude Code 등의 작업 상태가 세션 목록에 표시
- **tmux 치트시트** — `Ctrl+h`로 검색 가능한 tmux 단축키 목록
- **마우스 지원** — 클릭으로 패널 전환, 드래그로 크기 조절

---

## 제거

```bash
rm ~/.local/bin/tmux-manager
```
