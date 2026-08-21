# Robot Control Code Guide

## Scope

Use this guide only for feedback controllers, trajectory tracking, command
generation, `ros2_control` controllers, force or impedance control, and other
timing-sensitive control loops.

Use [`ros2-style.md`](ros2-style.md) for general ROS 2 behavior and
[`safety.md`](safety.md) whenever the change can publish motion commands, change
command ownership, or alter stop behavior.

## Time and update period

- Make the intended control rate and time source explicit.
- Use measured or framework-provided `dt` when the algorithm depends on elapsed
  time. Do not assume configured timer period equals actual execution period.
- Handle non-positive, non-finite, unexpectedly large, or discontinuous `dt`
  conservatively.
- Do not mix ROS time, simulation time, steady time, and wall time without a clear
  reason and conversion.
- Detect control-loop overruns when timing affects safety or performance.

## Units, frames, and signs

- Make controller input, error, state, and output units and frames explicit when
  ambiguity is possible.
- Keep degree/radian, position/velocity, linear/angular, and body/world conversions
  at clear boundaries.
- Verify quaternion ordering, angle wrapping, joint-axis direction, and sign
  conventions before tuning gains or reversing commands.
- Prefer names that expose ambiguous units or frames, such as `yaw_error_rad` or
  `velocity_command_base_mps`.

## State validity and freshness

Before using measurements or estimates, consider timestamp age and ordering,
transform availability, dropout, finite values, and physically implausible jumps.
Define the controller behavior for stale or invalid state; do not reuse old state or
commands indefinitely without a timeout policy.

## Limits and command shaping

- Apply the position, velocity, acceleration, jerk, effort, workspace, and hardware
  limits relevant to the commanded interface.
- Keep filtering, rate limiting, saturation, and final validation order explicit.
- Check the final command for invalid numeric values and relevant bounds before it
  reaches the actuator-facing boundary.
- Do not weaken URDF, controller, or hardware limits to hide unstable behavior.
- When one clamp can violate another constraint, test the combined behavior.

## Stateful controllers

For PID controllers, observers, filters, or other stateful control logic:

- define initialization and reset conditions,
- handle integral windup when outputs saturate,
- make derivative filtering and derivative-on-error or measurement choices explicit
  when relevant,
- do not carry incompatible state across mode changes,
- document parameter units and expected operating range.

Do not add advanced control structure when a simpler controller satisfies the
measured requirement. Record the observed behavior and test conditions when tuning.

## Mode transitions and command continuity

- Define the initial setpoint during activation or switching.
- Prevent discontinuous command jumps where practical.
- Reset or preserve controller state deliberately across activation, deactivation,
  reset, and mode changes.
- If switching fails or state becomes uncertain, use the stop or hold behavior
  defined by [`safety.md`](safety.md).

## High-rate paths

For controller `update()` methods and similar high-rate paths, keep work bounded and
avoid blocking operations, unbounded retries, excessive logging, and unnecessary
allocation when timing matters. Do not claim real-time safety unless the complete
execution path, synchronization, memory behavior, and dependencies have been
verified.

## Validation

Choose cases that exercise the changed controller behavior, such as:

- zero error and steady state,
- large initial error or setpoint step,
- saturation and anti-windup,
- stale, delayed, missing, or malformed state,
- irregular `dt` and loop overrun,
- start, stop, reset, cancellation, and mode transitions.

When performance matters, report relevant measures such as tracking error,
overshoot, settling behavior, command saturation, or loop-period jitter. Record the
controller parameters, rates, test conditions, checks not run, and remaining hardware
risk.
