# Contributing to Abaris

Abaris is security-critical infrastructure with deliberately narrow v0 claims.
Contributions must preserve that scope and avoid unsupported conclusions.

## Current contribution scope

Abaris is in a documentation and architectural-foundation phase. Appropriate
contributions include:

- refining paired-experiment semantics;
- clarifying oracle states and the conclusion matrix;
- improving isolation and network-policy requirements;
- improving hardened Git materialization requirements;
- improving evidence identity, provenance, and preservation;
- strengthening the threat model and security requirements; and
- proposing criteria for a curated public historical corpus.

Do not implement the CLI, select major dependencies, or expand v0 scope without
an accepted decision.

## Engineering priorities

1. Correctness.
2. Clarity.
3. Maintainability.
4. Operational safety.
5. Long-term quality.

Treat repositories, revisions, dependencies, setup behavior, reproducers,
oracle inputs, and workload outputs as untrusted.

## Claim discipline

Contributions must preserve these rules:

- observations are primary;
- conclusions are derived and conservative;
- `ABSENT` is not proof of universal remediation;
- `REPRODUCER_BLOCKED` is scoped to the exact recorded experiment;
- `CANDIDATE_ONLY_PRESENT` is a blocking asymmetry, not a verified regression;
- failures and ambiguity produce `INDETERMINATE`;
- broad regression, root-cause, variant, and affected-version claims are out of
  scope; and
- AI cannot authoritatively influence the deterministic core.

## Before proposing a change

- Read [VISION.md](VISION.md), [THREAT_MODEL.md](THREAT_MODEL.md),
  [SECURITY.md](SECURITY.md), [docs/architecture.md](docs/architecture.md), and
  [docs/decision-log.md](docs/decision-log.md).
- Confirm the change contains no private vulnerability information.
- Identify affected threats, requirements, observations, conclusions, and
  decisions.
- Keep the change focused and reviewable.

## Security-sensitive changes

Changes affecting local-path handling, source materialization, isolation,
mounts, secrets, sockets, network policy, resource limits, parsing, oracle
evaluation, evidence, conclusions, or export require:

- an explicit threat-model assessment;
- testable security requirements;
- negative and failure-mode tests when implementation begins;
- documented residual risk; and
- security-architecture review.

Do not propose a public stable schema until the representative-case
stabilization gate is complete.

## Documentation standards

- Use professional English.
- Distinguish requirements, assumptions, limitations, and future goals.
- Do not describe unimplemented controls as guarantees.
- Do not hide primary observations behind a derived conclusion.
- Use normative language deliberately.
- Record accepted and superseded decisions in the decision log.

## Implementation standards

When implementation is approved:

- fail closed when mandatory controls or evidence are unavailable;
- keep trusted control-plane logic outside the workload boundary;
- avoid implicit shell execution and ambiguous input interpretation;
- make input, environment, policy, and evidence identities explicit;
- enforce limits outside the workload;
- add table-driven tests for all observation pairs, conclusion invariants,
  bounded failure reasons, and fail-closed policy states; and
- document operational and rollback implications.

## Commit and pull request standards

Use small, coherent commits with precise professional English messages.
Conventional Commits are preferred when they fit naturally.

A pull request must explain:

- objective and scope;
- affected decisions and claim semantics;
- security risks and trade-offs;
- validation performed;
- residual risks; and
- intentionally deferred work.

## Reporting security issues

Do not report security issues publicly. Follow [SECURITY.md](SECURITY.md).
