# Robot Safety Guide

## Scope

Use this guide only for changes that can cause or sustain physical motion, change
command ownership, alter stop behavior, or affect autonomous execution.

General ROS 2 interface and callback rules belong in
[`ros2-style.md`](ros2-style.md). Controller timing and limit behavior belongs in
[`control-style.md`](control-style.md).

## Required principles

- Make the active command owner explicit.
- Define what happens on stop, cancellation, timeout, shutdown, and exception.
- Reject invalid state transitions before issuing a command.
- On stale, invalid, missing, or uncertain state, prefer a defined non-moving or
  otherwise safer state.
- Do not hide motion, retry, recovery, or command switching inside generic wrappers.
- Keep hardware and workspace limits intact unless an intentional, reviewed change
  updates every affected layer.
- Ensure cleanup paths cannot leave a previous motion command active unintentionally.

## First real-hardware test

Before commanding real hardware, verify the affected motion and stop paths with the
safest practical method available. Prefer this order:

1. inspect command ownership and state transitions,
2. test pure decision logic and boundaries,
3. build and run focused package tests,
4. validate without hardware or in simulation,
5. use reduced speed or force and a controlled workspace for the first hardware
   test,
6. return to normal operating limits only after expected stop and recovery behavior
   is confirmed.

Do not claim a behavior is hardware-safe based only on a successful build, planner
result, action success, or heartbeat.

## Review questions

- Can any failure path issue or preserve an unintended command?
- Is repeated, delayed, malformed, stale, or cancelled input handled safely?
- Is the command owner unambiguous during startup, switching, and shutdown?
- Can an exception, disconnect, or partial initialization leave motion enabled?
- Are timeout and recovery behaviors explicit?
- Does recovery move toward a safer state rather than continue from uncertain state?
- Are relevant physical limits still enforced at the final command boundary?

## Documentation

Document only safety assumptions that a maintainer must know to avoid unsafe
behavior, such as command ownership, required ordering, stop semantics, physical
limits, or recovery constraints. Follow
[`python-docstring-style.md`](python-docstring-style.md) for Python documentation.
