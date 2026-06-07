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
| ADR-021 | Superseded | Oracle evaluation, observation assignment, and failure-reason model |
| ADR-022 | Open | Nondeterminism and rerun policy |
| ADR-023 | Open | Evidence serialization, lifecycle, confidentiality, and authentication |
| ADR-024 | Accepted | `CANDIDATE_ONLY_PRESENT` is a blocking comparative conclusion |
| ADR-025 | Accepted | Networked reproduction uses a closed per-run reproducer-target network |
| ADR-026 | Accepted | Local Git source is materialized under a hardened no-implicit-execution policy |
| ADR-027 | Accepted | Public schema stability is gated on 3–5 representative historical cases |
| ADR-028 | Accepted | Observation assignment uses ordered eligibility gates and one atomic declarative oracle predicate |
| ADR-029 | Accepted | A finalized evidence bundle preserves a minimum auditable result and tamper-evident integrity record |
| ADR-030 | Accepted | The runner is a replaceable execution boundary, not a verdict authority |

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

**Status:** Superseded

Superseded by ADR-028, which resolves this decision for the first private-draft
executable specification increment. Future expansion of the oracle language or
failure-reason taxonomy requires a separate decision and threat-model review.

## ADR-022: Nondeterminism and reruns

**Status:** Open

Decision criteria:

- required rerun count;
- acceptable observation variation;
- handling of flaky setup or reproduction;
- when instability forces `INDETERMINATE`; and
- evidence presentation.

## ADR-023: Evidence serialization, lifecycle, confidentiality, and authentication

**Status:** Open

ADR-029 resolves the minimum conceptual contents, completeness boundary,
content-digest requirements, and redaction semantics for a finalized evidence
bundle.

Remaining decision criteria:

- final serialization and schema technology;
- retention and deletion;
- confidentiality and encryption;
- signing, key management, authentication, and non-repudiation;
- independent verification tooling; and
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

## ADR-028: Observation eligibility and atomic oracle predicates

**Status:** Accepted

**Dependencies:** ADR-003, ADR-004, ADR-008, ADR-009, ADR-013, ADR-014,
ADR-016, ADR-025, and ADR-027.

The trusted evaluator assigns one run observation through two ordered stages.

First, it applies fail-closed eligibility gates for mandatory controls,
comparison equivalence, supported conditions, execution status, and evidence
integrity. Invalid contracts or unavailable mandatory controls detected before
execution refuse the experiment and produce no observations. If one or more
gates fail or become unverifiable after execution begins, the affected run is
`INDETERMINATE` and receives exactly one primary bounded `failure_reason`. All
failure events and details remain separate evidence.

The primary reason is selected by this fixed precedence:

1. `INFRASTRUCTURE_FAILURE`
2. `COMPARISON_EQUIVALENCE_FAILURE`
3. `UNSUPPORTED_CONDITION`
4. `RESOURCE_LIMIT_EXCEEDED`
5. `TIMEOUT`
6. `TARGET_STARTUP_FAILURE`
7. `REPRODUCER_FAILURE`
8. `EVIDENCE_MISSING`
9. `EVIDENCE_TRUNCATED`
10. `EVIDENCE_INVALID`
11. `EVIDENCE_AMBIGUOUS`

`INFRASTRUCTURE_FAILURE` covers fail-closed materialization, isolation,
network-policy, collector, and evaluator-control failures. Their specific
causes and all lower-precedence events remain evidence rather than additional
failure reasons.

Second, only after every eligibility gate passes, the evaluator applies
exactly one atomic declarative predicate. Predicate truth produces `PRESENT`;
predicate falsehood produces `ABSENT`.

An exit code, signal, absent declared file, differing HTTP status, or unmatched
bounded content is not automatically a failure when it is the predicate's
subject and the trusted evidence required to evaluate it is complete and valid.

The first private-draft predicate set is:

- exact process exit code;
- exact process signal;
- bounded stdout or stderr regular-expression search;
- declared file existence;
- bounded declared-file content regular-expression search;
- declared closed-network HTTP response status; and
- bounded declared closed-network HTTP response body regular-expression
  search.

Regular-expression predicates require complete bounded UTF-8 evidence and a
restricted linear-time profile. Invalid oracle contracts are rejected before
execution and produce no observations. Arbitrary executable predicates,
workload-authoritative observations, compound or ordered predicates, scoring,
AI-dependent evaluation, oracle-initiated network traffic, and general-purpose
or unbounded regular expressions are unsupported.

Rationale: an atomic declarative predicate keeps the trusted evaluator small and
deterministic. Ordered eligibility gates prevent failure, ambiguity, or missing
evidence from silently becoming `ABSENT`. One primary failure reason preserves
deterministic classification while attributable evidence preserves the full
failure record.

Rejected alternatives:

- executable or workload-authoritative oracle logic, because it expands the
  trusted surface and permits observation forgery;
- Boolean or ordered predicate composition, because it adds unnecessary parser,
  precedence, and failure-semantics complexity before representative cases
  justify it;
- binary `PRESENT` or `ABSENT` evaluation with failures recorded only as
  metadata, because failure could collapse into `ABSENT`; and
- general-purpose or unbounded regular expressions, because they create denial
  of service and cross-engine determinism risk.

## ADR-029: Evidence bundle minimum contents and integrity model

**Status:** Accepted

**Dependencies:** ADR-003, ADR-004, ADR-009, ADR-013, ADR-014, ADR-015,
ADR-025, ADR-026, ADR-027, and ADR-028.

A finalized evidence bundle is a local, self-describing, auditable artifact for
one completed paired result. It preserves the declared experiment contract,
source and materialization identities, enforced policies, baseline and
candidate executions, assigned observations, bounded failure reasons,
captured artifacts, deterministic conclusion derivation, limitations, and
integrity metadata.

An evidence bundle is finalized only when it contains the minimum audit chain
needed to verify:

- the private-draft evidence-bundle contract identity and experiment-contract
  identity and digest;
- immutable subject identities and materialization controls;
- comparison, network, and resource-limit policies, plus environment and
  platform identity when available or explicit availability status otherwise;
- both execution and oracle-evaluation records;
- both assigned observations and any required primary `failure_reason`;
- the conclusion derived only from the normative observation-pair matrix; and
- the manifest, captured-artifact digests, explicit omissions, redactions,
  truncations, warnings, and limitations.

Invalid contracts, unavailable mandatory controls, or an incomplete minimum
audit chain detected before observation assignment produce no observations or
conclusion and cannot produce a finalized authoritative evidence bundle. They
may be preserved, when possible, as a distinct local, non-conclusive attempt
record. The attempt-record serialization and stable schema remain unresolved.

After execution begins, insufficient predicate evidence makes the affected
observation `INDETERMINATE` under ADR-028. A finalized bundle may preserve that
paired result only when the remaining minimum audit chain is complete. Missing
evidence that prevents audit of the contract, materialization, policies,
observation pair, conclusion derivation, or manifest integrity prevents an
authoritative bundle and conclusion; any retained partial material remains an
attempt record.

Every captured artifact has a content digest. The manifest references those
digests and has its own digest. This makes retained content tamper-evident; it
does not authenticate the producer, provide non-repudiation, prove
completeness or correctness, protect confidentiality, or make evidence safe to
publish.

Redaction is permitted only when it is explicit and attributable. The bundle
records what categories were preserved, transformed, redacted, or omitted and
retains digest relationships where applicable. Redaction must not silently
change an observation. Removing, changing, or making unavailable evidence
required by the oracle means the affected observation cannot remain `PRESENT`
or `ABSENT`; it becomes `INDETERMINATE` or is not produced, according to the
ADR-028 execution stage. Removing evidence required for the minimum audit chain
prevents an authoritative bundle.

The bundle preserves assigned observations and the conclusion derived from
them. It does not independently reinterpret raw execution records, advisory
metadata, or workload output, and raw logs cannot override an assigned
observation or conclusion.

The normative private-draft minimum-content fixture is
[adr-029-evidence-bundle-minimum.json](fixtures/adr-029-evidence-bundle-minimum.json).
It defines preserved evidence concepts, not human-report rendering, an
attempt-record schema, a public schema, or a compatibility promise.

Rationale: a bounded minimum makes results inspectable without prematurely
choosing a final format or protection system. Separating incomplete attempts
from finalized evidence bundles prevents missing audit evidence from being
presented as a conclusive result. Content addressing provides a useful
tamper-evidence baseline while preserving honest limits.

Rejected alternatives:

- treating every partial attempt as an evidence bundle, because missing audit
  evidence could be mistaken for a completed result;
- allowing the bundle to recalculate observations or conclusions from raw
  logs, because that would create a second conflicting authority;
- permitting silent or predicate-relevant redaction, because it could change
  result semantics without an explicit eligibility failure;
- requiring cryptographic signing in v0, because producer authentication, key
  management, and trust roots remain unresolved; and
- publishing a stable evidence schema now, because representative-case review
  remains required by ADR-027.

## ADR-030: Runner boundary and execution interface v0

**Status:** Accepted

**Dependencies:** ADR-003, ADR-004, ADR-008, ADR-009, ADR-012, ADR-013,
ADR-016, ADR-025, ADR-026, ADR-028, and ADR-029.

The runner is an execution boundary, not a verdict authority.

The core creates one bounded runner request for exactly one `baseline` or
`candidate` side and one declared execution phase. The runner attempts that
phase under the received policies and returns one factual, auditable execution
record plus captured-artifact references. The core coordinates multiple phases
and both sides. Phase names remain private-draft and illustrative; ADR-030 does
not create a stable phase taxonomy.

The core remains responsible for:

- loading and validating the reproduction contract;
- applying pre-execution eligibility rules and requiring mandatory controls;
- materializing source under ADR-026 or accepting a trusted materialization
  record from the responsible core component;
- deriving runner requests from the accepted contract;
- validating execution-record completeness, consistency, and policy status;
- evaluating the atomic declarative oracle predicate under ADR-028;
- assigning `PRESENT`, `ABSENT`, or `INDETERMINATE`;
- selecting the bounded normative `failure_reason` for an `INDETERMINATE`
  observation;
- deriving the conclusion only from the normative observation-pair matrix; and
- assembling or coordinating the authoritative evidence bundle under ADR-029,
  or refusing a conclusion when its minimum audit chain is incomplete.

The runner is responsible only for applying the received execution policies,
attempting the declared phase, and returning observable facts. A conceptual
runner request contains:

- run identity, side, and one private-draft illustrative phase;
- an already-materialized source reference;
- command identity, declared working directory, and declared environment;
- declared resource limits and network contract;
- expected target configuration when applicable;
- artifact-capture and redaction constraints; and
- mandatory controls that must be applied and reported.

Because the core relies on those execution facts and policy-status reports, a
supported runner is part of the future trusted computing base for factual
execution. That trust does not grant verdict authority. Concrete runner and
backend selection, trust analysis, and conformance testing remain deferred.

The runner must not fetch remotes, initialize submodules, acquire LFS content,
use object alternates, honor replace refs, acquire partial-clone or promisor
objects, rematerialize source, or otherwise weaken ADR-026.

The factual execution record contains, at minimum conceptually:

- side, execution-attempt identity, and runner identity when available or
  explicit identity-unavailability status;
- phase and command identity;
- effective execution parameters as audit facts, not as a stable runner API;
- applied-control and policy-enforcement status;
- start and end timestamps;
- process result, termination reason, timeout status, and resource-limit
  status;
- network-policy and materialized-source-access status;
- captured-evidence and artifact references compatible with ADR-029;
- infrastructure errors; and
- bounded warnings.

An execution record is an input to an evidence bundle, not the evidence bundle
itself. The runner does not assemble an authoritative evidence bundle,
reinterpret evidence, evaluate the oracle, assign an observation, select the
normative `failure_reason`, or derive a comparative conclusion. Runner-reported
failure labels and verdict-like labels are diagnostics only and are ignored by
the core for normative classification.

If a mandatory control cannot be applied before execution, the runner refuses
the attempt and reports that execution did not start. No observation or
comparative conclusion is produced; retained material may form only a
non-conclusive attempt record under ADR-029.

If the runner starts without a mandatory control, cannot report a mandatory
policy status, or reports a post-start infrastructure failure, the execution is
ineligible for an authoritative determinate observation. The core applies
ADR-028, does not evaluate the predicate, and assigns `INDETERMINATE` with the
applicable bounded normative `failure_reason`. Infrastructure failure must
never become `ABSENT`. The runner must prefer fail-closed behavior.

ADR-025 defines reproduction-network requirements; the runner applies them and
reports factual policy status. ADR-026 defines source materialization policy;
the runner receives an already-materialized reference and cannot weaken it.
ADR-028 defines oracle evaluation, observations, and normative failure
classification; the runner decides none of them. ADR-029 defines the minimum
authoritative evidence bundle; runner execution records and artifact references
are inputs to it, and the runner neither assembles the bundle nor recalculates
its conclusion.

The normative private-draft boundary fixture is
[adr-030-runner-boundary.json](fixtures/adr-030-runner-boundary.json). It
defines no stable runner API, phase taxonomy, backend contract, final
serialization, or public compatibility promise.

Rationale: a replaceable factual execution boundary keeps normative
observation and conclusion semantics independent of any specific runtime. It
also makes runner behavior testable through request and execution-record
fixtures without prematurely selecting an implementation.

Trade-offs:

- an abstract boundary reduces backend coupling but must remain bounded to
  avoid speculative architecture;
- deferring backend selection preserves substitutability but delays real
  isolation conformance testing;
- separating execution from verdict authority improves auditability but
  requires disciplined, complete execution records; and
- denying the runner observation authority reduces flexibility but protects
  Abaris's normative semantics.

Non-goals and deferred decisions:

- selecting or implementing Docker, Podman, local shell, a VM, Firecracker,
  gVisor, Kata, nsjail, or any other runner, backend, or isolation technology;
- selecting an implementation language, framework, validator, scheduler, CLI,
  storage system, or final serialization;
- defining a stable runner API, phase taxonomy, public schema, signature model,
  or CI integration;
- supporting private vulnerability handling, production execution, implicit
  external network access, remote source acquisition, LFS, or submodules; and
- allowing workload output, reproducer output, runner diagnostics, or raw logs
  to override normative observations or conclusions.

Rejected alternatives:

- allowing the runner to assign observations or conclusions, because that
  would make backend behavior a competing verdict authority;
- allowing one opaque side-wide request, because per-phase factual records are
  easier to audit and permit the core to retain orchestration authority;
- accepting omitted mandatory-policy status, because the core could not
  establish observation eligibility; and
- selecting a concrete backend now, because ADR-019 and implementation-specific
  conformance decisions remain unresolved.
