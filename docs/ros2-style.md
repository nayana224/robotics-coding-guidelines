# ROS 2 Style Guide

## Purpose

Use this guide for ROS 2 nodes, interfaces, parameters, QoS, TF, launch files,
package metadata, MoveIt 2 integrations, and other ROS-facing behavior.

This guide is language-independent. Combine it with
[`python-style.md`](python-style.md) for `rclpy` code or
[`cpp-style.md`](cpp-style.md) for `rclcpp` and C++ components.

## Preserve interfaces by default

Do not change the following unless the change is intentional and every affected
producer, consumer, launch file, configuration, test, and downstream package is
handled together:

- package names,
- node names,
- topic names and message types,
- service names and types,
- action names and types,
- parameter names, types, and defaults,
- QoS profiles,
- TF frame names and tree relationships,
- launch arguments and remappings,
- controller and plugin names,
- executable and library names,
- installed paths,
- units and coordinate-frame conventions.

Treat parameter renames, default-value changes, QoS changes, and frame changes as
interface changes even when the code still builds.

## Before changing an interface

Trace only the dependency path needed to establish impact:

1. Find the publisher, server, owner, or source of the interface.
2. Find direct subscribers, clients, action users, launch/config references, and
   tests.
3. Confirm message/service/action type, QoS, namespace, remapping, frame, and units.
4. Check whether external packages or hardware drivers consume the interface.
5. Decide whether backward compatibility must be preserved.

For multi-package or unclear runtime paths, also follow
[`repository-workflow.md`](repository-workflow.md).

## Nodes and callbacks

- Keep callbacks non-blocking.
- Do not perform long waits, unbounded retries, large file I/O, or blocking hardware
  I/O directly in executor callbacks without a deliberate architecture.
- Keep simple callbacks direct; separate non-trivial policy or conversion logic from
  ROS I/O when it improves clarity or testability.
- Make timer, future, action goal, process, subscription, device, and shutdown
  ownership explicit when relevant.
- Avoid excessive logging in high-rate callbacks.

## Executors, callback groups, and concurrency

When callbacks can run concurrently and share mutable state:

- identify the executor model and callback-group behavior,
- choose mutually exclusive or reentrant groups deliberately,
- minimize shared mutable state,
- keep lock scope small,
- do not hold a lock while waiting for a future, service, action, process, or
  hardware I/O,
- make cancellation and shutdown behavior explicit.

Do not add multi-threaded executors or callback-group complexity without a concrete
need.

## Parameters and configuration

- Validate required parameters before first use.
- Validate ranges, units, allowed values, and dependent settings at the configuration
  boundary.
- Keep parameter names explicit and domain-specific.
- Include units in names when ambiguity is likely.
- Do not duplicate the same configuration value across files without a clear owner.
- Avoid turning stable implementation constants into parameters without a real need
  for deployment-time variation.
- Keep example configuration free of credentials and machine-specific secrets.

## QoS

- Preserve existing QoS unless there is a demonstrated compatibility or behavior
  reason to change it.
- Choose reliability, durability, history, and depth deliberately.
- Document non-default QoS when interoperability is not self-evident.
- When debugging missing messages, compare publisher and subscriber QoS before
  changing unrelated code.

## TF and coordinate frames

- Preserve frame names and parent-child relationships unless intentionally changing
  the system contract.
- Make source and target frames explicit in transformations.
- Keep units SI unless an external interface defines otherwise.
- Do not mix camera optical, robot base, tool, map, odom, or object frames implicitly.
- Verify transform timestamp assumptions for time-sensitive sensor data.
- Treat frame changes as system-level changes and trace all affected consumers.

## Topics, services, and actions

- Use topics for streaming state or data, services for bounded request-response work,
  and actions for long-running, feedback-producing, cancellable operations when the
  existing architecture supports that model.
- Do not change communication primitives merely for stylistic preference.
- Preserve cancellation, timeout, and failure semantics when modifying actions or
  service-based workflows.
- Keep command ownership explicit when more than one component may produce motion or
  actuator commands.

## Launch files

- Keep launch files declarative and inspectable.
- Avoid `OpaqueFunction` or substantial runtime Python logic unless standard launch
  actions and substitutions cannot express the requirement clearly.
- Do not create launch arguments for values that are not expected to vary.
- Keep a node's parameters, remappings, namespace, conditions, and output behavior
  visible near the node declaration when practical.
- Split launch files only for reusable subsystems or independently launchable
  workflows.
- Do not hide safety-relevant overrides, command ownership, controller selection, or
  remappings behind unnecessary helper layers.

## Package metadata and build files

- Add only dependencies directly required by the package.
- Keep `package.xml` and `CMakeLists.txt` consistent with the existing repository
  conventions.
- Preserve target names, exported interfaces, package names, executable names,
  library names, and install paths unless all downstream users are updated.
- Remove a dependency only after its final use is removed.
- Avoid complex build macros unless they are meaningfully reused and simplify each
  caller.

## MoveIt 2

For MoveIt 2 work:

- preserve planning group, joint, link, frame, controller, and plugin names,
- check SRDF, URDF/Xacro, controller configuration, kinematics configuration,
  planning pipelines, and launch references when a change crosses those boundaries,
- keep planning requests, execution, cancellation, and controller ownership explicit,
- prefer supported MoveIt 2 APIs over internal implementation details,
- validate planning and execution behavior in simulation before commanding real
  hardware when practical.

Use [`cpp-style.md`](cpp-style.md) for C++ extensions and
[`safety.md`](safety.md) for real robot execution changes.

## ros2_control and control loops

For controller update loops, hardware interfaces, trajectory tracking, command
limits, or timing-sensitive behavior, also follow
[`control-style.md`](control-style.md) and [`safety.md`](safety.md).

## Hardware and drivers

For camera drivers, robot drivers, grippers, sensors, or vendor SDK wrappers:

- preserve device-facing interface assumptions unless intentionally updated,
- handle timeout, disconnect, stale data, partial initialization, and shutdown,
- distinguish configuration errors from transient runtime failures,
- keep reconnect or retry behavior bounded and visible,
- avoid silently continuing when state validity is unknown.

## Validation

Start with the smallest relevant check and expand only as needed. Depending on the
change, this may include:

- package build,
- targeted unit tests,
- `colcon test` for affected packages,
- launch parsing/startup,
- topic/service/action interface inspection,
- TF tree or transform checks,
- simulation or no-hardware execution,
- then controlled hardware validation when needed.

Do not treat a successful build as proof that QoS, frames, controller ownership,
timing, or hardware safety remain correct. Record checks that could not run and the
remaining risk.
