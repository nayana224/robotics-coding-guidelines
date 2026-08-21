# Python Style Guide

## Purpose

Use this guide for Python implementation work. Python is the default implementation
language for new code unless the existing project, runtime interface, vendor SDK,
performance requirement, or user request gives a concrete reason to use another
language.

Follow the project's existing Python configuration first. Do not reformat or
modernize unrelated code only to satisfy this guide.

## Language choice

Prefer Python for:

- algorithms and application logic,
- data processing and experiment scripts,
- PyTorch and machine-learning workflows,
- tooling and automation,
- ROS 2 nodes already implemented with `rclpy`,
- prototypes whose timing and interface requirements are satisfied by Python.

Do not rewrite an existing C++ component in Python merely because Python is the
default. Preserve the current stack when it is already appropriate.

## General style

- Follow PEP 8 as interpreted by the project's configured formatter and linter.
- Prefer readable, explicit control flow over dense expressions and clever Python.
- Prefer functions when no meaningful state, resource ownership, or lifecycle exists.
- Use classes when they own meaningful state, invariants, resources, or lifecycle.
- Avoid static-method-only classes and generic `Manager`, `Helper`, `Utils`, or
  `Processor` abstractions when a domain-specific function or type is clearer.
- Avoid metaprogramming, reflection, dynamic attribute access, and hidden import-time
  side effects unless the existing framework requires them.

## Type hints

Use type hints when they reduce ambiguity at important boundaries, especially for:

- public functions and methods,
- structured data shared across modules,
- non-trivial return values,
- optional values,
- values whose units or accepted forms are otherwise unclear.

Do not mechanically annotate every local variable. Avoid introducing `Protocol`,
`Generic`, `TypeVar`, overload sets, or wrapper types for one concrete use.

Type hints do not replace runtime validation of untrusted ROS messages, files,
parameters, network input, or hardware responses.

## Data structures

- Prefer built-in containers for simple local data.
- Use `dataclass` when a real structured configuration or state object benefits from
  named fields and explicit ownership.
- Do not introduce a dataclass only to reduce a function's visible parameter count.
- Avoid deeply nested dictionaries when a stable domain structure would be clearer.
- Preserve existing serialized formats and field names unless all consumers are
  intentionally updated.

## Functions and side effects

For non-trivial functions that consume input and change state, prefer this flow:

1. Validate input and preconditions.
2. Convert or prepare data.
3. Execute one cohesive domain action.
4. Return a result or report a contextual error.

Keep important side effects explicit. Do not hide hardware commands, file writes,
network operations, state transitions, or expensive computation inside properties,
destructors, imports, or opaque decorators.

## Exceptions and logging

- Catch exceptions only where the code can add context, recover, translate the
  failure, or guarantee cleanup.
- Do not use broad exception handling to continue from an unknown state.
- Include actionable context in errors without exposing credentials or sensitive
  values.
- Avoid repeated logs in high-rate loops or callbacks.

## Concurrency

Do not introduce `asyncio`, threads, multiprocessing, or background workers without
a concrete need.

When concurrency is required:

- make ownership and shutdown explicit,
- avoid shared mutable state where practical,
- document ordering or synchronization assumptions that are not obvious,
- ensure exceptions and cancellation do not leave hardware or stateful resources in
  an unsafe state.

## NumPy and PyTorch

- Make tensor and array shapes clear at non-trivial interfaces.
- Document units and coordinate frames when represented numerically.
- Avoid unnecessary copies in performance-sensitive paths, but do not trade clarity
  for micro-optimizations without evidence.
- Make device and dtype assumptions explicit when they affect correctness.
- Do not silently move tensors between CPU and accelerator devices.
- Separate training-only behavior from inference paths when practical.
- Preserve reproducibility settings when modifying experiment or training code.

## ROS 2 Python

For `rclpy`, launch, parameters, QoS, TF, actions, services, topics, or ROS package
behavior, also follow [`ros2-style.md`](ros2-style.md).

For robot motion or hardware-sensitive behavior, also follow
[`safety.md`](safety.md). For controller or timing-sensitive work, follow
[`control-style.md`](control-style.md).

## Comments and docstrings

Write identifiers and technical terms in English. Write comments and docstrings in
concise, natural Korean when additional explanation is useful.

Follow [`python-docstring-style.md`](python-docstring-style.md) for detailed Python
docstring rules.

## Validation

Use the project's existing checks first. Depending on the project, useful checks may
include formatter/linter, unit tests, import checks, package tests, or targeted runtime
checks.

Do not install or upgrade tools merely to satisfy this guide unless requested. Record
checks that could not run and the remaining risk.
