---
name: pr-review
description: Review pull requests for correctness, regression risk, and missing failure/concurrency coverage; use for code review and review-readiness checks.
metadata:
  short-description: Review PR correctness and failure cases
---

# Pull Request Review

Review the proposed behavior against its requirements and invariants. Focus findings on defects the diff introduces or leaves unguarded; do not turn preferences or hypothetical redesigns into blockers.

## Review workflow

1. Read the PR description, changed files, nearby implementation, and relevant tests. Identify the invariant each changed path is supposed to preserve.
2. Trace normal, error, retry, timeout, and concurrent execution paths through the actual transaction and service boundaries.
3. Check authorization and resource ownership at every API boundary, including internal service endpoints.
4. Inspect tests for assertions about observable outcomes and persisted side effects, not only return values.
5. Report actionable findings first. For each finding, name the file/line, triggering scenario, user or data impact, and a concrete correction. Separate verified defects from questions and suggestions.
6. Summarize coverage gaps and verification actually performed. State clearly when a test was skipped or could not run; a successful compile is not evidence that an integration behavior passed.

## Failure and concurrency checklist

Apply the relevant cases to the operation under review; skip cases that do not fit its state or guarantees.

- **Concurrent duplicate request:** start two requests together with the same idempotency key, not just two sequential calls. Assert one durable operation and exactly one set of side effects.
- **Idempotency boundary:** verify key scope, payload mismatch behavior, key retention, and that the key-to-operation mapping is durable across process restart. A retry after a lost response must identify the original operation.
- **Check-lock-check:** when correctness depends on a lock, inspect whether the idempotency/state condition is rechecked after acquiring it. A pre-lock check alone permits races.
- **Atomic effects:** verify related writes commit together or roll back together. Assert final balances/records and paired ledger entries, not only a `success` response.
- **Competing operations:** run distinct transfers against the same balance concurrently and assert the balance cannot go below its allowed minimum. Inspect lock ordering and isolation for deadlocks and overspending.
- **Failure between steps:** exercise timeouts and crashes before/after the durable write and before/after response/event delivery. Retries and reconciliation must not duplicate or lose the operation.
- **Boundary and rejection cases:** cover insufficient funds, invalid state, currency mismatch, duplicate provider callback, malformed amount, and unauthorized or non-owned resource IDs where applicable.
- **Real dependency behavior:** concurrency, locking, isolation, and migration claims must be tested against the production database or a compatible integration environment. An in-memory substitute may hide those defects.
- **CI evidence:** ensure required integration tests run in CI. If local infrastructure is missing and tests are skipped, report that as unverified rather than passed.

## Review comments

Prefer one focused finding per comment. Explain the invariant and the concrete failure path; avoid vague requests such as “add more tests.” For example: “Two requests can both pass this pre-lock existence check. Start them concurrently with the same key and assert that only one ledger posting commits; recheck the key after the lock.”

When the change is sound, summarize what the added test proves and identify any remaining unverified cases without overstating production readiness.
