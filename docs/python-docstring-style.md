# Python Comments and Docstrings

## Scope

Use this guide only to decide when Python comments and docstrings add useful
information. General Python implementation rules belong in
[`python-style.md`](python-style.md).

## Language

- Write comments and docstrings in concise, natural Korean.
- Keep identifiers, API names, library names, ROS interface names, units,
  coordinate frames, and established technical terms in English.
- Prefer the original technical term when a Korean translation would be less clear.

## Comments

Add a comment only when it explains information that the code cannot express well,
such as:

- why a decision or workaround exists,
- an important assumption or constraint,
- required ordering or timing,
- units or coordinate frames,
- hardware or compatibility behavior,
- or a safety implication.

Do not narrate obvious code, comment every line, leave commented-out code, or use
comments to compensate for vague naming.

```python
# 센서 SDK는 depth를 mm로 반환하므로 여기서 m로 변환한다.
depth_m = depth_mm * 0.001
```

## Docstrings

Use docstrings when they clarify a public contract or non-obvious behavior.
Prioritize:

- public functions and classes,
- ROS-facing behavior,
- non-trivial state transitions,
- safety-relevant side effects,
- units, frames, ranges, ownership, blocking behavior, or exceptions that are not
  obvious from the signature.

Do not add docstrings mechanically to short private helpers or repeat names and type
hints without adding semantic information. Prefer a one-line docstring when one
sentence is enough.

```python
def transform_point(point: Point3D, transform: Transform) -> Point3D:
    """카메라 좌표계의 점을 robot base 좌표계로 변환한다."""
```

For a public function with important side effects:

```python
def publish_stop_command(reason: StopReason) -> None:
    """로봇에 안전 정지 명령을 발행한다.

    Args:
        reason: 안전 정지가 필요한 원인.

    Raises:
        RuntimeError: 정지 명령 발행에 실패한 경우.
    """
```

## TODO and workaround comments

- Avoid vague TODOs such as `TODO: improve this`.
- State the concrete missing work and why it remains.
- Include an issue number when the repository workflow supports it.
- Use `FIXME` for known incorrect behavior.
- Use `HACK` only for an unavoidable workaround and state when it can be removed.

```python
# TODO(#42): firmware가 SI 단위를 제공하면 이 변환을 제거한다.
```
