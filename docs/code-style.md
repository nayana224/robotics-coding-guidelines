# Code Style Guide

## Goal

Keep code easy to read, review, test, and modify without obscuring robotics,
ROS 2, or hardware behavior.

This file contains language-independent rules. Use the more specific guides only
when relevant:

- Python: [`python-style.md`](python-style.md)
- C/C++: [`cpp-style.md`](cpp-style.md)
- ROS 2: [`ros2-style.md`](ros2-style.md)
- Controllers and timing-sensitive loops: [`control-style.md`](control-style.md)
- Robot and hardware safety: [`safety.md`](safety.md)
- Python comments and docstrings: [`python-docstring-style.md`](python-docstring-style.md)

Follow the existing project's formatter, linter, build configuration, and local
conventions before introducing a new style.

## Rule levels

- **Required**: apply to new or modified code unless a documented exception is
  necessary.
- **Recommended**: apply when it improves clarity without unnecessary indirection.

Apply only the rules relevant to the changed files and behavior.

## Design principles

1. **Required:** Make the smallest change that satisfies the request.
2. **Required:** Preserve public interfaces and saved-data formats unless all affected
   users are intentionally updated together.
3. **Required:** Keep safety-sensitive behavior explicit when present: command
   ownership, stop behavior, state transitions, and failure recovery must not be
   hidden.
4. **Recommended:** Prefer explicit, domain-specific names over clever abstractions.
5. **Recommended:** Separate pure policy, conversion, and validation logic from I/O
   when the boundary is non-trivial, reusable, safety-relevant, or clearly improves
   testing.
6. **Required:** Prefer the solution with the fewest concepts needed for the current
   task, not necessarily the fewest lines.
7. **Required:** Do not refactor, rename, reformat, or clean up unrelated code.

## Function design

For non-trivial functions that accept input and change state, prefer this flow:

1. Validate input and preconditions.
2. Prepare or convert data.
3. Execute one cohesive domain action.
4. Return a result or report a contextual error.

Do not force this structure onto simple getters, cleanup methods, or thin adapters.

Split a function when one or more of these conditions apply:

- it combines independent policy decisions and external I/O,
- the main control flow cannot be understood in one pass,
- it has unrelated externally visible side effects,
- a non-trivial pure portion can be tested independently,
- nested conditions make success, failure, or stop behavior unclear.

Do not split a function only to satisfy a line-count guideline. Avoid helpers that
merely rename one or two obvious statements.

## Class design

Create a class when it owns meaningful state, invariants, resources, lifecycle, or a
stable external-system boundary.

- Prefer functions when no meaningful state or lifecycle exists.
- Avoid static-method-only classes.
- Prefer composition over inheritance.
- Use inheritance only when substitution is real and the base contract is clear.
- Do not introduce an abstract base class for one implementation or hypothetical
  future variants.

## Naming

Use names that explain intent and domain meaning.

Avoid vague structural names such as `Manager`, `Processor`, `Handler`, `Helper`,
`Utils`, `Common`, `Factory`, `Base`, or `Context` when a specific domain name is
available.

Keep identifiers, API names, technical terms, ROS interfaces, units, and coordinate
frames in English. Include units in variable or configuration names when ambiguity is
likely, such as `timeout_seconds` or `max_speed_mps`.

Do not create generic `utils`, `helpers`, or `common` modules when a domain-specific
module name is available.

## Abstractions and wrappers

Use an abstraction when it creates a useful boundary, for example when it:

- isolates an external API or hardware dependency,
- separates I/O from testable core logic,
- gives domain meaning to a repeated operation,
- makes a safety or state-transition contract clearer,
- enforces an important invariant.

Do not add wrappers that only rename a single call, hide important side effects, or
add navigation without simplifying the caller.

Do not introduce factories, registries, plugin systems, dependency-injection layers,
or extension hooks for hypothetical reuse.

## Validation boundaries

Validate untrusted data at clear boundaries such as:

- ROS messages and parameters,
- files and serialized data,
- network or serial input,
- public APIs,
- hardware and vendor SDK responses.

After validation, internal helpers may rely on the documented invariant. Do not repeat
the same defensive checks throughout every internal function.

Do not add checks for impossible states without evidence that they can occur. Avoid
broad exception handling where the code cannot recover meaningfully.

## Error handling and logging

- **Required:** Do not silently swallow failures.
- **Required:** Include actionable context in errors when safe to log.
- **Required:** Make failure and safe-stop paths explicit for code that can command
  motion or affect autonomous behavior.
- **Recommended:** Log important state changes once and avoid repeated logs in
  high-rate paths.
- Do not expose credentials, tokens, or sensitive infrastructure details in logs.

## Comments and documentation

Comments should explain reasons, constraints, ordering, units, frames, workarounds,
or safety implications rather than narrating obvious code.

Write comments and docstrings in concise, natural Korean. Keep identifiers, official
API names, libraries, ROS interfaces, units, coordinate frames, and established
technical terms in English.

- Do not comment every assignment, branch, or function call.
- Do not restate code in natural language.
- Prefer clearer names and control flow over explanatory comments.
- Do not add docstrings mechanically to every symbol.
- Do not leave commented-out code; use version control.
- Update or remove comments when behavior changes.
- Avoid vague TODOs; state the concrete missing work and why it remains.

## Configuration files

- Preserve the project's existing format and indentation conventions.
- Keep names explicit and domain-specific.
- Include units in names when values could be misread.
- Keep related settings together without unnecessary nesting.
- Do not keep obsolete, duplicated, or commented-out configuration.
- Treat renames and default-value changes as interface changes when external users
  depend on them.
- Validate ranges, units, required keys, and dependent settings at the loading
  boundary.
- Do not duplicate the same configuration value across files without a clear owner.

For ROS 2 parameters, launch files, QoS, TF, package metadata, and build behavior,
follow [`ros2-style.md`](ros2-style.md).

## Generated files

- Do not edit generated files directly.
- Edit the source schema, template, Xacro, message definition, or generator input.
- Do not commit build output, caches, logs, or temporary generated files unless the
  repository explicitly versions them.
- When generated artifacts are versioned, record the source and regeneration command.
- Review regenerated output for unexpected interface, frame, message, or
  configuration changes.

## Secrets and environment-specific values

- Do not commit credentials, tokens, private keys, passwords, or sensitive URLs.
- Avoid hard-coded user home paths, machine-specific absolute paths, device serial
  numbers, and fixed network addresses when they vary by deployment.
- Store genuinely variable values in documented configuration or environment
  variables.
- Keep safe defaults explicit and validate required deployment-specific values.
- Do not turn a stable implementation constant into configuration without a real
  variation need.

## Tests

- Test externally meaningful behavior, state transitions, boundary values, and
  relevant failure paths.
- Keep each test focused on one behavior and name it by condition and expected result.
- Avoid tests that only duplicate implementation details.
- Prefer pure logic, small fakes, or lightweight fixtures over broad mocking.
- Do not weaken assertions, skip tests, or broaden tolerances only to make a failure
  pass.
- For hardware-sensitive behavior, identify a no-hardware, simulation, or dry-run
  check before real-device testing when practical.

## Validation tools

Run the most relevant checks already available in the project environment.

- Start with the smallest useful scope and expand only when needed.
- Do not install tools, upgrade dependencies, or change the environment unless
  requested.
- If a recommended check cannot run, record the missing check, the reason, and the
  remaining risk.
- Formatting-only validation does not replace runtime, interface, configuration, or
  hardware-safety checks.

## Self-review before completion

- Re-read the diff, not only the final files.
- Confirm every changed line is required by the request.
- Check for accidental interface, unit, frame, default-value, and behavior changes.
- Check that names describe domain intent and comments explain reasons.
- For stateful or safety-sensitive changes, confirm success, failure, cancellation,
  cleanup, and safe-stop paths remain explicit.
- Report tests not run and assumptions that remain unverified.
