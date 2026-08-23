# C and C++ Style Guide

## Purpose

Use this guide for C and C++ implementation work, especially ROS 2 components,
hardware interfaces, vendor SDK integrations, MoveIt 2 extensions, and
performance- or timing-sensitive code.

C or C++ is not selected merely because hardware is involved. Prefer it when the
existing stack or interface is C/C++-native, when a supported vendor API requires
it, or when measured timing and performance requirements justify it.

## Language choice

Prefer C or C++ when one or more of the following is true:

- the existing ROS 2 package is implemented with `rclcpp`,
- the component implements or extends `ros2_control` hardware interfaces,
- MoveIt 2 or another C++-native ROS 2 component must be extended directly,
- the vendor SDK's primary or supported integration path is C/C++,
- deterministic timing, latency, throughput, or memory behavior materially matters,
- the existing public interface, plugin system, ABI, or build system requires it.

Do not rewrite a working Python component in C++ without a concrete requirement.
Preserve the language and architecture already used by the affected package when
that choice remains appropriate.

## General style

- Follow the C++ standard, compiler options, formatter, and linter already configured
  by the project.
- Prefer straightforward control flow over clever expressions, deep nesting, or
  compressed logic.
- Prefer ordinary functions and concrete types over unnecessary generic machinery.
- Prefer composition over inheritance.
- Avoid deep inheritance, complex templates, macros, and metaprogramming unless they
  solve a demonstrated problem better than simpler language features.
- Keep headers focused on interfaces and move implementation details to source files
  when practical.
- Treat enabled compiler warnings as issues to resolve rather than suppress by
  default.

## Ownership and lifetime

- Prefer RAII for resources and cleanup.
- Avoid raw owning pointers.
- Use `std::unique_ptr` for exclusive ownership when dynamic lifetime is required.
- Use `std::shared_ptr` only when ownership is genuinely shared; do not use it as a
  default pointer type.
- Use references or non-owning pointers when lifetime is externally guaranteed and
  the relationship is clear.
- Make callback, thread, timer, device, and SDK resource lifetimes explicit.
- Ensure partial initialization failures release already-acquired resources safely.

## Constness and interfaces

- Use `const` when it communicates an invariant or prevents unintended mutation.
- Prefer narrow interfaces that expose domain intent rather than implementation
  details.
- Preserve public headers, plugin names, exported symbols, message types, and ABI/API
  expectations unless the change is intentional and all affected users are handled.
- Do not add wrapper classes that only rename a single SDK or ROS call.

## Error handling

- Check return codes and error states from hardware and vendor SDK APIs.
- Add contextual error information at the boundary where it is useful.
- Do not continue operating hardware after a failure that invalidates device or robot
  state.
- Make cleanup and safe-stop behavior explicit for command-producing components.
- Avoid exceptions across interfaces where the surrounding framework or ABI does not
  define safe exception propagation.

## Concurrency and callbacks

- Make thread ownership, shutdown, and synchronization explicit.
- Avoid holding a mutex while waiting for service/action results, futures, blocking
  hardware I/O, or external processes.
- Keep critical sections small.
- Avoid hidden background threads in wrappers unless the underlying SDK requires them
  and the lifecycle is documented.
- For ROS 2 executors and callback groups, also follow
  [`ros2-style.md`](ros2-style.md).

## Real-time and high-rate code

When deterministic timing matters:

- avoid blocking I/O and unbounded retries,
- avoid repeated dynamic allocation in the hot path when practical,
- avoid per-cycle logging,
- keep execution time bounded,
- move expensive preparation outside the loop,
- do not claim real-time safety without checking the complete call path.

For controller update loops, trajectory tracking, command generation, or
`ros2_control`, also follow [`control-style.md`](control-style.md).

## Hardware and SDK integration

- Keep device initialization, configuration, start, stop, and cleanup phases explicit.
- Validate required device capabilities and configuration before enabling commands.
- Treat disconnect, timeout, stale data, partial initialization, and shutdown as
  normal failure cases that require defined behavior.
- Avoid hard-coded device serial numbers, addresses, or machine-specific paths when
  they vary by deployment.
- Prefer the vendor's documented supported API over undocumented internal behavior.

For robot motion or safety-sensitive hardware behavior, also follow
[`safety.md`](safety.md).

## ROS 2 C++

For `rclcpp`, parameters, QoS, TF, actions, services, topics, pluginlib, MoveIt 2,
launch, or ROS package behavior, also follow [`ros2-style.md`](ros2-style.md).

## Comments and documentation

Keep identifiers, API names, ROS interfaces, units, frames, and technical terms in
English. Write comments in concise, natural Korean when they explain non-obvious
reasoning, constraints, ownership, timing, units, frames, workarounds, or safety
behavior.

## Validation

Use the checks already provided by the project. Depending on the package, this may
include targeted builds, compiler warnings, unit tests, `colcon test`, plugin loading,
no-hardware execution, simulation, or a focused integration test.

Do not install or upgrade build tools merely to satisfy this guide unless requested.
Record checks that could not run and the remaining risk.
