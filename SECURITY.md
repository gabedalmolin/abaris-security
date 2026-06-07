# Security Policy

## Project maturity

Abaris is in a documentation and architectural-foundation phase. No released
implementation or supported execution environment exists.

All security controls described here are requirements, not guarantees.

## Reporting a security issue

Do not open a public issue for suspected vulnerabilities in Abaris, its planned
execution model, or any non-public vulnerability information associated with
the project.

Use GitHub private vulnerability reporting when it is enabled. Before the first
implementation release, the project must publish a stable private contact
method and response expectations.

Reports should include:

- affected document, component, or decision;
- impact and preconditions;
- safe reproduction information and evidence;
- known mitigations; and
- disclosure constraints.

Do not include unrelated secrets, personal data, or third-party vulnerability
details that you are not authorized to share.

## Responsible-use policy

Abaris is intended for authorized maintainer-controlled experiments against
local repositories.

Users must:

- be authorized to test the repository, revisions, dependencies, and
  environment;
- use only curated, already-public historical vulnerability cases in v0;
- use disposable isolation appropriate for hostile code;
- protect evidence and review every export; and
- preserve the constrained meaning of observations and conclusions.

Users must not:

- test third-party or production systems;
- use Abaris for discovery, exploit generation, or automatic disclosure;
- use private vulnerability reports in v0;
- expose host secrets, home-directory mounts, or a Docker or equivalent
  host-control socket to untrusted execution;
- describe `ABSENT` as proof that a vulnerability is fixed; or
- describe `REPRODUCER_BLOCKED` as universal patch validation.

## Required execution posture

A future supported run must:

- accept only a local repository path;
- resolve immutable baseline and candidate source identities;
- use separate disposable isolated environments;
- expose no ambient host secrets;
- expose no home-directory mount;
- expose no Docker or equivalent host-control socket;
- apply an explicit setup-network policy;
- deny external reproduction egress;
- allow only declared reproducer-target communication for networked cases;
- enforce CPU, memory, process, disk, output, and wall-clock limits;
- collect and evaluate evidence outside workload authority; and
- fail closed when mandatory controls or evidence are unavailable.

Containers alone are not a sufficient hostile-code security boundary.

## Observation and claim safety

- `PRESENT` requires successful trusted evaluation of the defined oracle.
- `ABSENT` requires successful trusted evaluation that does not demonstrate the
  defined presence condition.
- Execution failure, missing evidence, oracle ambiguity, timeout, truncation,
  unsupported behavior, or control failure must produce `INDETERMINATE`.
- Every `INDETERMINATE` observation must include a bounded technical
  `failure_reason` separate from the paired conclusion.
- Paired observations are the primary result.
- Conclusions are constrained summaries and must include limitations.

Abaris does not verify root cause, variants, affected versions, duplicates,
broad regressions, or universal patch correctness.

## Evidence handling and publication

Evidence may contain source code, dependency information, traces, crash inputs,
or vulnerability details. It must remain local by default.

- An authoritative evidence bundle exists only when its minimum audit chain
  permits verification of the contract, applied policies, both observations,
  and conclusion derivation.
- Incomplete pre-observation attempts may be retained only as distinct,
  non-conclusive attempt records.
- Captured artifacts must be content-addressed and attributable; the manifest
  must reference their digests and have its own digest.
- Missing, transformed, redacted, omitted, or truncated content must be
  explicit.
- Redaction that removes, changes, or makes predicate-relevant evidence
  unavailable prevents the affected observation from remaining `PRESENT` or
  `ABSENT`; it becomes `INDETERMINATE` or is not produced, according to the
  execution stage.
- Restrictive local permissions and a retention policy are required.
- No publication or export occurs without explicit human action.
- Public historical status must be verified before any corpus item is shared.

Content addressing provides tamper evidence. It does not authenticate the
producer, provide non-repudiation, prove completeness or correctness, protect
confidentiality, or make evidence safe to publish. Signing, key management,
authentication, independent verification tooling, and safe export review
remain unresolved.

## Setup network

Setup network access is a distinct risk boundary. An explicit policy is
required for every run.

Enabling access can:

- expose data;
- introduce mutable dependencies;
- allow attacks against external systems; and
- make the experiment non-reproducible.

The exact supported v0 setup-network modes remain unresolved.

For networked reproduction cases, the supported profile is a closed per-run
network containing only the declared reproducer and target. It permits only
declared reproducer-initiated target connections and response traffic. It
denies external egress, host gateway access, cloud metadata, external DNS,
published host ports, undeclared ingress, and undeclared peers. Non-network
cases receive no reproduction network.

Runtime-provided "internal network" modes are not sufficient by themselves.
The backend must verify that host and gateway access are also denied.

The minimum deny policy includes link-local metadata space and known metadata
or runtime-host endpoints, including `169.254.0.0/16`,
`169.254.169.254/32`, `fd00:ec2::254/128`, `fd20:ce::254/128`,
`metadata.google.internal`, `host.docker.internal`, and
`gateway.docker.internal`.

## Git materialization

Git materialization is data extraction, not project execution.

v0 materialization must:

- read only from the operator-supplied local repository;
- run non-interactively in an Abaris-controlled temporary directory;
- ignore user, global, system, and untrusted repository-local configuration
  where possible;
- disable hooks, clean/smudge/process filters, credential helpers, LFS
  downloads, submodule initialization, and external protocols;
- reject object alternates, promisor or partial-clone acquisition, replace
  refs, and undeclared external object resolution;
- reject unsafe symlinks before execution;
- preserve separate immutable baseline and candidate trees; and
- record commit identity, tree identity, Git version, and materialization
  policy;
- record requested and enforced controls, rejected features, violations, and
  final outcome; and
- refuse execution when mandatory materialization controls or identities cannot
  be verified.

Submodules, LFS objects, and remote source acquisition are unsupported by
default in v0.

## Schema maturity

Abaris may use private draft contracts to execute representative historical
cases. No schema is public or stable until 3–5 representative cases have
completed review. Draft contracts must be clearly marked disposable and must
not create compatibility promises. Every draft artifact must self-identify as
`private-draft` with compatibility set to `none`.

## Deterministic core

The trusted core that validates inputs, evaluates oracle evidence, assigns
observations, and derives conclusions must not depend on AI. Optional future AI
assistance must be separable, non-authoritative, and unable to change a
deterministic result.

## Disclosure workflow

1. Report Abaris security issues privately.
2. Validate impact and reproduction safely.
3. Prepare mitigations and tests.
4. Coordinate disclosure timing and content.
5. Publish only after explicit human approval.

Abaris must never automate disclosure.
