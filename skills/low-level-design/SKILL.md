---
name: low-level-design
description: Coach or implement object-oriented low-level designs for machine-coding and interview problems using requirements, domain modeling, SOLID, design patterns, UML, and concurrency reasoning.
---

# Low-level design

Use this skill for LLD/OOD questions, machine-coding exercises, class design, and reviews of object-oriented designs. Optimize for a small, coherent model that satisfies explicit requirements and can evolve; do not force patterns or build distributed-system infrastructure unless the request needs it.

## Design workflow

1. Clarify the use cases, actors, commands, queries, invariants, failure cases, scale assumptions, and whether the output is an interview sketch or production code. State assumptions when the prompt is silent.
2. Separate requirements into domain rules, application orchestration, and infrastructure concerns. Identify the nouns that own state and the verbs that change it; keep domain objects responsible for their invariants.
3. Model relationships deliberately: association for a link, aggregation for a shared part, and composition when the owner controls the part's lifecycle. Prefer composition and interfaces over inheritance unless substitutability is real.
4. Define public APIs and state transitions before implementation. Give each operation clear preconditions, postconditions, return values, and error behavior. Keep constructors and methods valid-by-default.
5. Apply SOLID, DRY, KISS, and YAGNI as decision tests, not slogans. Introduce an interface or pattern only when it isolates a variation, boundary, or test seam that the current design actually has.
6. Choose patterns by the problem they solve: factories for creation variation, strategy for interchangeable algorithms, observer or pub/sub for notification, state for behavior changing with lifecycle, command for queued/undoable actions, adapter for incompatible APIs, decorator for layered behavior, and facade for a simpler boundary. Explain the tradeoff and avoid pattern names without a concrete role.
7. Show collaboration with a compact class diagram or text diagram, then walk through the main use case end to end. For non-trivial behavior, add a sequence or state diagram.
8. If concurrency matters, name the shared mutable state, protect the smallest critical section, define lock ownership/order, and reason about atomicity, visibility, retries, idempotency, deadlock, starvation, and shutdown. Prefer immutable values and single ownership where possible.
9. Validate with happy-path, boundary, invalid-transition, repeat-call, failure/retry, and concurrent scenarios. Identify focused tests that would catch regressions, and note what is intentionally out of scope.

## Problem-solving shape

For interview-style answers, present: assumptions; use cases; core entities and responsibilities; relationships; APIs and invariants; design-pattern rationale; a main-flow walkthrough; extension points; concurrency and failure handling; and tests. Keep the first design minimal, then extend it for named follow-ups such as multiple pricing rules, new vehicle types, cancellation, persistence, or thread safety.

For code tasks, keep domain logic testable without I/O, inject clocks/randomness/external clients when they affect behavior, avoid global mutable state and speculative abstractions, and preserve the repository's language and conventions. Include executable examples or tests when the surrounding project supports them.

## Review checklist

- Every requirement maps to a collaborator or explicit out-of-scope decision.
- Invariants have one clear owner; callers cannot bypass them through public mutable state.
- Abstractions correspond to real variation and have substitutable implementations.
- Dependencies point toward stable domain policy rather than concrete infrastructure.
- State transitions, errors, retries, and duplicate requests are defined.
- The design remains understandable without relying on a pattern catalog.

Do not treat this as a system-design skill: discuss distributed topology, capacity planning, or deployment only when it directly constrains the object model.
