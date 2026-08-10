# Documentation Structure and Maintenance

## Scope

프로젝트 README, package README, `docs/` 문서를 새로 만들거나 정리할 때 사용합니다.

목표는 문서를 많이 만드는 것이 아니라, **각 정보의 기준 문서(Single Source of Truth)를 명확히 두고 중복과 오래된 문서를 줄이는 것**입니다.

작은 오탈자 수정이나 한두 줄 설명 보완에는 이 문서를 전부 적용할 필요가 없습니다.

---

## 1. 먼저 문서 역할을 나눈다

기본 구조는 다음을 우선합니다.

```text
README.md
  → 프로젝트 진입점 / 가장 짧은 시작 방법 / 문서 링크

src/<package>/README.md 또는 <package>/README.md
  → package 책임 / executable / input / output / config / package 사용법

docs/*.md
  → 설치 / 운영 절차 / 시스템 설계 / 이론 / calibration / 결과 해석

docs/references.md
  → 공식 문서 / 외부 URL / 논문 / 참고자료
```

전역 `README.md`에 모든 세부 절차와 이론을 복사하지 않습니다.

---

## 2. 전역 README는 index로 유지한다

전역 `README.md`는 새 사용자가 다음을 빠르게 찾을 수 있을 정도로만 작성합니다.

- 프로젝트 목적과 범위
- 요구 환경
- clone / build의 최소 예시
- 초기 설정으로 가는 링크
- package 목록과 각 package README 링크
- 주요 `docs/` 문서 링크
- 데이터 저장 원칙
- 참고자료 링크

상세 명령, 긴 troubleshooting, 수식 설명, calibration 결과 해석은 해당 기준 문서로 이동합니다.

---

## 3. 주제마다 기준 문서 하나를 정한다

같은 내용을 여러 문서에 복사하지 않습니다.

예:

```text
SDK 설치
  → docs/sdk_installation.md

workspace build / troubleshooting
  → docs/build_and_run.md

카메라 초기 설정
  → docs/camera_setup.md

camera model 이론
  → docs/camera_model.md

calibration 절차
  → docs/calibration_procedure.md

calibration 결과 해석
  → docs/calibration_validation.md
```

다른 README나 문서에서는 필요한 결론만 짧게 적고 기준 문서로 링크합니다.

한 주제의 기준 문서를 변경할 때는 그 문서를 가리키는 링크와 요약이 여전히 맞는지 확인합니다.

---

## 4. 설치와 운영 절차를 분리한다

외부 SDK, driver, toolchain처럼 프로젝트 밖 dependency의 설치는 별도 문서로 분리합니다.

설치 문서에는 다음을 포함합니다.

- 공식 source / repository / vendor URL
- 지원 OS / architecture / version 조건
- 권장 설치 경로
- 설치 명령
- 설치 확인 명령
- 다른 경로를 사용할 때의 설정 방법
- version / commit / release 기록 방법

프로젝트의 초기 설정 문서에는 이미 dependency가 설치되어 있다고 가정하고 실제 장비와 config를 준비하는 절차만 둡니다.

---

## 5. package README는 package 책임만 설명한다

package README에는 다음을 우선합니다.

- package 목적
- 주요 executable / library
- 각 executable의 역할
- 주요 input / output / config
- 다른 package와의 관계
- package 책임에서 제외되는 항목

공통 workspace build 명령, 공통 SDK 설치, 공통 이론을 package README마다 반복하지 않습니다. 공통 문서로 링크합니다.

---

## 6. 이론과 실제 절차를 분리한다

가능하면 다음을 한 문서에 섞지 않습니다.

```text
왜 그렇게 하는가
  → theory / design / interpretation 문서

어떻게 실행하는가
  → setup / procedure / package README
```

예를 들어 calibration에서는 다음처럼 나눌 수 있습니다.

```text
camera model / 좌표계 / 수학적 관계
  → 이론 문서

capture → solve → validate 순서
  → 절차 문서

RMS / reprojection / outlier / physical validation 의미
  → 결과 해석 문서
```

---

## 7. 문서 정리 전에 실제 repository 상태를 확인한다

문서를 수정하거나 삭제하기 전에 다음을 확인합니다.

1. 현재 branch의 실제 파일 내용
2. 실제 package / executable / config 이름
3. 현재 runtime에서 사용하는 경로
4. 문서가 가리키는 파일과 command가 실제로 존재하는지
5. 다른 문서가 해당 파일을 링크하는지

오래된 계획이나 과거 prototype 설명을 현재 구현처럼 유지하지 않습니다.

코드가 이미 구현되었는데 문서에 `향후 작업`으로 남아 있거나, 삭제된 executable / 옛 data path를 설명하는 문서는 정리 후보입니다.

---

## 8. 중복 문서는 통합하거나 삭제한다

문서를 새로 추가하기 전에 기존 문서로 흡수할 수 있는지 확인합니다.

삭제 후보:

- 다른 기준 문서와 내용이 대부분 겹침
- 현재 runtime에서 쓰지 않는 prototype 절차만 설명
- 오래된 path / command / architecture를 설명
- 같은 주제의 새 문서가 이미 기준 문서가 됨
- package README와 동일한 내용을 반복

삭제할 때는 반드시 먼저:

1. 현재 파일 내용을 확인하고,
2. 필요한 고유 정보가 다른 문서에 보존되었는지 확인하고,
3. repository 전체에서 해당 문서 링크를 검색하고,
4. 링크를 수정한 뒤,
5. 파일을 삭제합니다.

문서 삭제만으로 깨진 link를 남기지 않습니다.

---

## 9. 문서 이름과 구조는 단순하게 유지한다

파일명은 역할을 알 수 있게 정합니다.

좋은 예:

```text
docs/sdk_installation.md
docs/build_and_run.md
docs/data_layout.md
docs/eye_to_hand_calibration.md
docs/handeye_result_interpretation.md
docs/references.md
```

단순한 프로젝트에서는 `docs/` 아래를 지나치게 여러 하위 폴더로 나누지 않습니다. 문서가 충분히 많아져 탐색성이 실제로 떨어질 때만 `setup/`, `theory/`, `calibration/` 같은 하위 폴더를 도입합니다.

기존 파일명을 바꾸면 모든 내부 link를 함께 갱신합니다.

---

## 10. generated data와 문서를 분리한다

다음은 일반적으로 `docs/`에 복사하지 않습니다.

- capture image
- PLY / NPY / BIN
- calibration sample
- solver raw result
- runtime log
- 임시 backup

문서에는 저장 위치, schema, 해석 방법만 설명합니다.

재현을 위해 결과 예시가 필요하면 최소한의 대표 값만 넣고, 실제 생성 데이터의 canonical 위치를 링크 또는 경로로 안내합니다.

---

## 11. 외부 참고자료는 한 곳에 모은다

공식 SDK 문서, ROS / OpenCV 문서, 논문, vendor guide는 가능하면 `docs/references.md`에 모읍니다.

개별 문서에서는 필요한 reference만 링크하고, 같은 URL 목록을 여러 파일에 반복하지 않습니다.

설치에 필수적인 공식 source URL은 설치 문서에도 직접 남길 수 있습니다.

---

## 12. 문서 변경 검증

문서 변경 후 최소한 다음을 확인합니다.

- Markdown link가 존재하는 파일을 가리키는지
- executable / package / config 이름이 현재 코드와 일치하는지
- 삭제한 문서를 가리키는 link가 남아 있지 않은지
- 동일한 상세 설명이 여러 파일에 불필요하게 남지 않았는지
- `README.md`가 index 역할을 유지하는지
- code / config를 문서 정리와 무관하게 변경하지 않았는지

문서 정리 작업에서는 코드 동작을 바꾸지 않는 것이 원칙입니다.

---

## 13. 변경 보고

문서 정리 후 다음을 간단히 보고합니다.

- 새로 만든 기준 문서
- 중복을 제거한 문서
- 삭제한 오래된 문서와 삭제 이유
- 이동하거나 수정한 주요 link
- 코드 / config 변경 여부
- 아직 정리하지 않은 문서가 있다면 그 이유
