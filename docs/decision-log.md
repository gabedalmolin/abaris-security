# Abaris Decision Log

This log records accepted, proposed, open, and superseded architectural
decisions.

Status values:

- `Accepted`: current project direction.
- `Proposed`: recommended but not fully accepted.
- `Open`: intentionally unresolved.
- `Superseded`: replaced by a later decision.

## Decision index

| ID | Status | Decision |
| --- | --- | --- |
| ADR-001 | Accepted | Abaris is a maintainer-controlled patch-behavior validation and evidence-preservation harness |
| ADR-002 | Accepted | v0 performs one paired experiment using one baseline, one candidate, one reproducer, and one oracle |
| ADR-003 | Accepted | Observations are primary and use explicit three-state semantics |
| ADR-004 | Accepted | Conclusions are conservative deterministic derivations from paired observations |
| ADR-005 | Accepted | `REPRODUCER_BLOCKED` is not a universal fix claim |
| ADR-006 | Accepted | v0 accepts only local repository paths |
| ADR-007 | Accepted | v0 uses only curated, already-public historical cases |
| ADR-008 | Accepted | Curated inputs remain untrusted executable content |
| ADR-009 | Accepted | Baseline and candidate runs must satisfy a comparison-equivalence contract |
| ADR-010 | Accepted | Setup and reproduction network policies are separate and explicit |
| ADR-011 | Accepted | External reproduction egress is denied and service-target traffic is explicitly constrained |
| ADR-012 | Accepted | Untrusted execution receives no host secrets, home mount, or Docker socket |
| ADR-013 | Accepted | CPU, memory, process, disk, output, and wall-clock limits are mandatory |
| ADR-014 | Accepted | Input identities are immutable and retained evidence is content-addressed |
| ADR-015 | Accepted | Publication is human-controlled |
| ADR-016 | Accepted | The deterministic core has no AI dependency |
| ADR-017 | Accepted | v0 exclusions are enforceable product boundaries |
| ADR-018 | Open | First supported target ecosystem |
| ADR-019 | Open | Qualifying isolation backend and host support |
| ADR-020 | Open | Setup-network policy modes and dependency acquisition |
| ADR-021 | Open | Oracle evaluation, observation assignment, and failure-reason model |
| ADR-022 | Open | Nondeterminism and rerun policy |
| ADR-023 | Open | Evidence schema and protection |
| ADR-024 | Accepted | `CANDIDATE_ONLY_PRESENT` is a blocking comparative conclusion |
| ADR-025 | Accepted | Networked reproduction uses a closed per-run reproducer-target network |
| ADR-026 | Accepted | Local Git source is materialized under a hardened no-implicit-execution policy |
| ADR-027 | Accepted | Public schema stability is gated on 3–5 representative historical cases |

## ADR-001: Narrow product identity

**Status:** Accepted

Abaris is a maintainer-controlled patch-behavior validation and
evidence-preservation harness.

It does not claim universal vulnerability remediation or general patch
correctness.

Rationale: this describes the defensible result the system can actually produce
and prevents the product from drifting into unsupported scanner or verification
claims.

## ADR-002: One paired experiment

**Status:** Accepted

v0 compares one known-vulnerable baseline revision and one candidate revision
using one supplied reproducer and one vulnerability-presence oracle.

Rationale: this is the smallest useful experiment that preserves a clear causal
comparison without claiming root-cause or affected-range knowledge.

## ADR-003: Three-state observations

**Status:** Accepted

Each run produces exactly one primary observation:

- `PRESENT`
- `ABSENT`
- `INDETERMINATE`

`INDETERMINATE` is mandatory when reliable evaluation is impossible. Failure or
missing evidence must never silently become `ABSENT`.

## ADR-004: Deterministic conclusion matrix

**Status:** Accepted

Conclusions are derived only from the paired observations:

| Baseline | Candidate | Conclusion |
| --- | --- | --- |
| `PRESENT` | `ABSENT` | `REPRODUCER_BLOCKED` |
| `PRESENT` | `PRESENT` | `REPRODUCER_STILL_SUCCEEDS` |
| `ABSENT` | `ABSENT` | `BASELINE_NOT_REPRODUCED` |
| `ABSENT` | `PRESENT` | `CANDIDATE_ONLY_PRESENT` |
| Any pair containing `INDETERMINATE` | Any pair containing `INDETERMINATE` | `INCONCLUSIVE` |

The full nine-pair matrix remains normative in
[architecture.md](architecture.md#conclusion-matrix).

Rationale: conclusion labels cannot safely replace the richer paired
observations. Indeterminate cases remain `INCONCLUSIVE`; a valid candidate-only
asymmetry is preserved explicitly.

## ADR-005: Constrained meaning of `REPRODUCER_BLOCKED`

**Status:** Accepted

`REPRODUCER_BLOCKED` means only that the oracle was `PRESENT` on baseline and
`ABSENT` on candidate under recorded conditions.

It does not mean universally fixed, root cause corrected, variants addressed,
or regressions excluded.

Rationale: the term is convenient but otherwise likely to be overstated.

## ADR-006: Local repository paths only

**Status:** Accepted

v0 accepts only operator-supplied local repository paths.

This prevents Abaris from becoming a remote target acquisition or scanning
tool. A local path remains untrusted and must not be mounted directly into
untrusted execution.

## ADR-007: Curated public historical corpus

**Status:** Accepted

v0 uses only curated cases for vulnerabilities that have already been publicly
disclosed.

Rationale: private-report and community-intake workflows require governance,
access control, disclosure, and trust mechanisms that v0 does not provide.

## ADR-008: Curation does not imply executable trust

**Status:** Accepted

Repositories, setup behavior, dependencies, reproducers, oracle inputs, and
artifacts remain untrusted even when curated.

Rationale: historical vulnerable code and reproduction material can be
malicious or unsafe independent of governance quality.

## ADR-009: Comparison-equivalence contract

**Status:** Accepted

Baseline and candidate runs use the same reproducer, oracle semantics, policy,
environment contract, limits, evidence requirements, and permitted difference
rules. The source revision is intentionally different.

Revision-owned setup outcomes may differ, but all differences are evidence and
may make the result indeterminate.

Rationale: “same reproducer” alone is insufficient for a defensible paired
comparison.

## ADR-010: Separate network policies

**Status:** Accepted

Setup and reproduction have separate explicit network policies.

Rationale: setup may need dependency acquisition, while reproduction network
access creates a different and generally unacceptable target and exfiltration
risk.

## ADR-011: Constrained reproduction networking

**Status:** Accepted

Non-network cases receive no reproduction network. Service-target cases may
receive only the closed declared reproducer-target network defined by ADR-025.
External reproduction egress remains denied.

Any future exception requires a new decision and threat-model update.

## ADR-012: Mandatory host-resource exclusions

**Status:** Accepted

Untrusted execution receives no host secrets, home-directory mount, Docker or
equivalent host-control socket, undeclared writable host path, unnecessary
device, privilege, or capability.

Containers alone are not considered a sufficient hostile-code boundary.

## ADR-013: Mandatory resource limits

**Status:** Accepted

CPU, memory, process, disk, output, and wall-clock limits are externally
enforced for every run. Violations are evidence and may cause
`INDETERMINATE`.

## ADR-014: Immutable identities and content-addressed evidence

**Status:** Accepted

Accepted runs record immutable identities for source snapshots, reproducer,
oracle, environment contract, policy, and declared setup inputs. Retained
evidence objects are content-addressed.

Content addressing demonstrates identity and integrity, not correctness,
completeness, confidentiality, or safety.

## ADR-015: Human-controlled publication

**Status:** Accepted

Evidence and conclusions remain local until a human explicitly exports or
publishes them. Automatic disclosure is out of scope.

## ADR-016: No AI dependency in deterministic core

**Status:** Accepted

Input validation, evidence evaluation, observation assignment, and conclusion
derivation have no AI dependency.

Future optional AI assistance must be non-authoritative and separable.

## ADR-017: Explicit v0 exclusions

**Status:** Accepted

v0 excludes private vulnerability reports, untrusted community reproducers,
vulnerability discovery, exploit generation, root-cause verification, variant
analysis, affected-version inference, duplicate detection, automatic patch
generation, automatic disclosure, hosted execution, and broad regression
claims.

Expanding an exclusion requires an explicit product decision and threat-model
review.

## ADR-018: First supported target ecosystem

**Status:** Open

The previous Python-on-Linux target direction is reopened because it is not part
of the consolidated direction.

Decision criteria:

- availability of safe, public historical cases;
- setup reproducibility;
- isolation requirements;
- dependency acquisition risk;
- oracle expressiveness; and
- maintainer usefulness.

## ADR-019: Isolation backend and host support

**Status:** Open

Decision criteria:

- verifiable exclusion of secrets, home mounts, and host-control sockets;
- disposable lifecycle;
- network and resource enforcement;
- local-first usability;
- trusted computing base; and
- failure observability.

## ADR-020: Setup network and dependency acquisition

**Status:** Open

Decision criteria:

- deny-by-default feasibility;
- supported restricted-access modes;
- destination control and evidence;
- immutable dependency identity;
- caching and offline operation; and
- exfiltration and third-party risk.

## ADR-021: Oracle evaluation and observation model

**Status:** Open

Decision criteria:

- deterministic three-state evaluation;
- clear distinction between `ABSENT` and failure;
- supported evidence predicates, including exit status, bounded output matching,
  declared files, process outcomes, and closed-network HTTP responses;
- explicit handling of timeout, crash, target startup failure, reproducer
  failure, missing evidence, and ambiguous evidence;
- a bounded `failure_reason` taxonomy that is required for `INDETERMINATE` and
  remains separate from observations and conclusions;
- minimal trusted evaluator surface;
- expressiveness for curated historical cases;
- bounded parsing and execution; and
- preserved evaluation provenance.

## ADR-022: Nondeterminism and reruns

**Status:** Open

Decision criteria:

- required rerun count;
- acceptable observation variation;
- handling of flaky setup or reproduction;
- when instability forces `INDETERMINATE`; and
- evidence presentation.

## ADR-023: Evidence schema and protection

**Status:** Open

Decision criteria:

- manifest and content-addressing model;
- retention and deletion;
- redaction and truncation;
- encryption and signing;
- independent verification; and
- safe export review.

Public schema stability is separately governed by ADR-027.

## ADR-024: Candidate-only presence

**Status:** Accepted

When the baseline observation is `ABSENT` and the candidate observation is
`PRESENT`, the derived conclusion is `CANDIDATE_ONLY_PRESENT`.

This is a blocking comparative conclusion. It records a valid asymmetry and
requires human review, but it does not assert a regression, vulnerability
introduction, exploitability, severity, or root cause.

Rationale: collapsing a valid asymmetry into `INCONCLUSIVE` loses useful
evidence. Calling it `REGRESSION_DETECTED` would overstate what the experiment
proved.

## ADR-025: Closed reproduction service network

**Status:** Accepted

Networked reproduction uses a new closed per-run network containing only the
declared reproducer and target participants.

Allowed:

- reproducer-initiated connections to declared target aliases and ports; and
- response traffic for those connections.

Denied:

- external egress;
- host and gateway access;
- cloud metadata and external DNS;
- at minimum the metadata ranges and endpoints `169.254.0.0/16`,
  `169.254.169.254/32`, `fd00:ec2::254/128`, `fd20:ce::254/128`,
  `metadata.google.internal`, `host.docker.internal`, and
  `gateway.docker.internal`;
- target-initiated connections to the reproducer unless separately modeled;
- undeclared peers and ingress;
- published host ports; and
- IPv6 unless explicitly modeled and equivalently restricted.

Non-network cases receive no reproduction network. The run fails closed if the
backend cannot enforce the policy.

Rationale: ordinary runtime network defaults and internal-network modes do not
by themselves prevent host or gateway communication. This ADR defines required
behavior without selecting Docker or another runtime as the qualifying
isolation backend.

## ADR-026: Hardened local Git materialization

**Status:** Accepted

v0 materializes baseline and candidate source from the operator-supplied local
repository under an Abaris-controlled, non-interactive Git policy.

Disabled or rejected by default:

- inherited system, global, user, and untrusted repository-local configuration
  where possible;
- hooks;
- clean, smudge, and process filters;
- credential helpers and prompts;
- LFS smudge and downloads;
- submodule initialization and recursion;
- external protocols and remote helpers;
- object alternates, promisor or partial-clone acquisition, replace refs, and
  other undeclared external object resolution; and
- unsafe symlinks.

Direct tree-object extraction with explicit file-type and symlink validation is
preferred. A sanitized checkout is only a possible bounded fallback. Source
materialization must not mutate the operator's working tree.

Every materialization attempt records its requested and enforced controls,
source identities, materializer and Git versions, rejected unsupported
features, violations, and final outcome. Execution is refused when mandatory
materialization controls or identities cannot be verified.

Rationale: Git materialization can trigger executable and network behavior.
`git clone && git checkout` is not treated as harmless data extraction.

Technical basis:

- [Git hooks](https://git-scm.com/docs/githooks) documents executable hooks and
  configurable `core.hooksPath`.
- [Git configuration](https://git-scm.com/docs/git-config) documents filters,
  credentials, and protocol policy.
- [Git submodules](https://git-scm.com/docs/git-submodule) documents cloning,
  fetching, worktree updates, recursion, and custom update-command behavior.
- [Git repository layout](https://git-scm.com/docs/gitrepository-layout)
  documents alternate object stores and replace refs.
- [Git partial clone](https://git-scm.com/docs/partial-clone) documents dynamic
  fetching of missing objects from promisor remotes.

## ADR-027: Schema stabilization gate

**Status:** Accepted

Abaris may use private, disposable draft experiment, oracle, and evidence
contracts immediately. It must not publish a stable schema or compatibility
promise until 3–5 representative historical cases complete review.

Every draft contract must self-identify as `private-draft` and explicitly state
that it provides no compatibility guarantee.

The case set must collectively include:

- a networked target;
- a non-network CLI or file-input reproducer;
- a process crash, exception, or signal;
- dependency or materialization sensitivity;
- a fixed-candidate result;
- a failed or inconclusive result; and
- candidate-only presence.

Rationale: representative evidence should shape oracle grammar, network
declarations, evidence taxonomy, and failure categories before public
stability. The gate must not be used to expand v0 into broad interoperability,
disclosure, duplicate-detection, or regression-generation schemas.
