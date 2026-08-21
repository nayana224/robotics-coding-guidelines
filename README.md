# Robotics Coding Guidelines

AI-assisted robotics software development를 위한 재사용 가능한 코딩·리뷰·안전 지침 모음입니다.

이 저장소는 논문 공부법이나 일반적인 ChatGPT 응답 스타일을 정의하는 곳이 아닙니다. 실제 프로젝트에서 AI가 Python, C/C++, ROS 2, MoveIt 2, `ros2_control`, 카메라·센서·로봇 SDK, URDF/Xacro, 제어 및 하드웨어 관련 코드를 읽고 수정할 때 따를 engineering guideline을 제공합니다.

`AGENTS.md`는 항상 핵심 지도 역할을 하고, `docs/`에서는 현재 작업과 직접 관련된 문서만 확인합니다. 모든 문서를 매번 읽는 방식이 아닙니다.

## 기본 언어 정책

Python을 새 코드의 기본 구현 언어로 사용합니다. 다만 다음과 같은 경우에는 기존 C/C++ stack을 유지하거나 C/C++을 선택합니다.

- 기존 ROS 2 package가 `rclcpp` 기반인 경우
- `ros2_control` hardware interface나 C++ plugin interface를 구현하는 경우
- MoveIt 2 또는 다른 C++-native component를 직접 확장하는 경우
- vendor SDK의 공식·주요 API가 C/C++인 경우
- 측정된 timing, latency, throughput 요구사항이 C/C++을 필요로 하는 경우

하드웨어를 사용한다는 이유만으로 C++을 선택하지 않고, 반대로 Python이 기본이라는 이유만으로 정상적인 C++ component를 Python으로 다시 작성하지 않습니다.

## 가장 쉬운 설치 방법

새 workspace로 이동합니다.

```bash
cd ~/inpyo_ws/new_robot_ws
```

아래 명령어를 실행합니다.

```bash
curl -fsSL \
  https://raw.githubusercontent.com/nayana224/robotics-coding-guidelines/main/bootstrap.sh \
  | bash
```

설치 후 다음 파일들이 생성됩니다.

```text
new_robot_ws/
├── AGENTS.md
└── docs/
    ├── code-style.md
    ├── python-style.md
    ├── cpp-style.md
    ├── ros2-style.md
    ├── code-review.md
    ├── commit-style.md
    ├── repository-workflow.md
    ├── documentation-style.md
    ├── control-style.md
    ├── python-docstring-style.md
    ├── urdf-xacro-style.md
    └── safety.md
```

이후 Codex나 GPT에게 다음처럼 요청할 수 있습니다.

```text
이 workspace의 AGENTS.md를 먼저 따라줘.
현재 작업에 직접 관련된 docs 문서만 확인해줘.
```

### 기존 지침 교체

기존 `AGENTS.md` 또는 `docs/`가 있으면 기본 설치는 덮어쓰지 않습니다. 완전히 교체할 때만 다음 명령을 사용합니다.

```bash
curl -fsSL \
  https://raw.githubusercontent.com/nayana224/robotics-coding-guidelines/main/bootstrap.sh \
  | bash -s -- . --force
```

`--force`는 기존 지침을 삭제하고 새 버전으로 교체합니다. 프로젝트별 규칙을 직접 작성했다면 먼저 백업하거나 병합합니다.

## 어떤 문서를 읽는가

| 작업 | 확인할 문서 |
|---|---|
| 일반적인 코드 수정 | `AGENTS.md`, `code-style.md` |
| Python / NumPy / PyTorch | `python-style.md` |
| C/C++ / vendor SDK | `cpp-style.md` |
| ROS 2 / MoveIt 2 / QoS / TF / launch | `ros2-style.md` + 해당 언어 guide |
| 코드 리뷰 | `code-review.md` |
| 커밋 생성 | `commit-style.md` |
| 여러 ROS package가 연결된 대형 저장소 | `repository-workflow.md` |
| README / package README / `docs/` 정리 | `documentation-style.md` |
| PID, trajectory tracking, `ros2_control` 등 제어 코드 | `control-style.md`, `safety.md` |
| URDF, Xacro, SDF | `urdf-xacro-style.md`, 필요 시 `safety.md` |
| motion command와 실제 하드웨어 | `safety.md` |

예를 들어 순수 Python 데이터 처리 함수 수정이라면 `AGENTS.md` + `code-style.md` + `python-style.md`면 충분합니다. MoveIt 2 C++ component라면 `cpp-style.md`와 `ros2-style.md`를 함께 확인합니다. `ros2_control` hardware interface나 실제 로봇 command path까지 포함되면 `control-style.md`와 `safety.md`를 추가합니다.

## 핵심 원칙

- 요청한 범위만 작게 수정
- 관련 없는 리팩터링, 이름 변경, 포맷 정리 금지
- 기존 project convention과 public interface 보존
- Python은 기본 언어지만 기존 stack과 interface를 우선 존중
- ROS interface, QoS, TF frame, controller 이름, 단위, launch argument 보존
- command ownership, stop behavior, state transition, failure recovery를 명시적으로 유지
- 실제 하드웨어 전에 가능한 simulation, dry-run, no-hardware 검증 우선
- 문서는 주제별 Single Source of Truth를 두고 중복 설명 제거
- 커밋은 `<type>: <한글 요약>` 형식
- 실행한 검증, 실행하지 못한 검증, 남은 위험 보고

## 요청 예시

Python 작업:

```text
AGENTS.md, docs/code-style.md, docs/python-style.md를 따라 수정해줘.
현재 구조와 public interface를 유지하고 요청한 부분만 최소한으로 변경해줘.
```

ROS 2 C++ / MoveIt 2 작업:

```text
AGENTS.md, docs/cpp-style.md, docs/ros2-style.md를 따라 작업해줘.
관련 package와 runtime path를 먼저 확인하고 ROS interface, QoS, TF, controller 이름은 의도하지 않는 한 변경하지 마.
```

제어·하드웨어 작업:

```text
AGENTS.md, docs/ros2-style.md, docs/control-style.md, docs/safety.md를 따라 수정해줘.
control period, frame, limit, timeout, command ownership, safe-stop을 확인하고 실제 하드웨어 전에 가능한 simulation 검증을 먼저 해줘.
```

코드 리뷰:

```text
AGENTS.md와 docs/code-review.md를 따라 리뷰해줘.
변경과 관련된 실제 오류, 안전 문제, 유지보수 위험만 우선적으로 보고해줘.
```

## 직접 저장소를 보관하는 방법

지침을 자주 수정할 때만 홈 디렉터리에 clone해 둡니다.

```bash
mkdir -p ~/.local/share
git clone \
  https://github.com/nayana224/robotics-coding-guidelines.git \
  ~/.local/share/robotics-coding-guidelines
```

최신화:

```bash
git -C ~/.local/share/robotics-coding-guidelines pull --ff-only
```

새 workspace에 적용:

```bash
bash ~/.local/share/robotics-coding-guidelines/install.sh ~/inpyo_ws/new_robot_ws
```

## 저장소의 역할

이 저장소에는 여러 robotics project에 반복 적용할 coding/engineering rule만 둡니다.

특정 robot, camera model, ROS distribution, workspace path, topic name, TF tree, dataset definition처럼 project-specific한 정보는 각 실제 project의 `AGENTS.md`, `README.md`, `docs/`, config에 둡니다.

논문 읽기·학습 방식처럼 coding과 독립적인 instruction은 별도 repository에서 관리하는 것을 권장합니다.
