# Abaris Reproducer Specification

## Status

Version: `private-draft`

This document defines the implementation-neutral v0 experiment contract. It is
not a stable format, parser, or execution implementation.

This is a private, disposable draft contract. It creates no public compatibility
promise. The final serialization and schema technology remain unresolved.
Examples use JSON-compatible concepts for clarity.

No public stable schema may be released until 3–5 representative historical
cases have completed review.

## Purpose

The experiment specification binds:

- one local repository source;
- one baseline revision;
- one candidate revision;
- one supplied reproducer;
- one vulnerability-presence oracle;
- one comparison-equivalence contract;
- explicit setup and reproduction network policies;
- explicit resource limits; and
- required evidence.

It must provide enough information for the trusted core to run the paired
experiment, assign two observations, and derive one conservative conclusion.

## Trust model

The specification, repository, revisions, setup steps, reproducer, fixtures,
oracle inputs, and workload outputs are untrusted.

Validation must:

- reject unknown fields by default;
- enforce size, nesting, count, path, and value limits;
- reject path traversal and absolute host-path access;
- reject host secrets, home-directory mounts, and Docker socket exposure;
- avoid implicit shell evaluation and ambient environment variables;
- require explicit network and resource policy; and
- keep observation assignment outside workload authority.

## Top-level model

| Field | Required | Meaning |
| --- | --- | --- |
| `schema` | Yes | Contract identity, `private-draft` stability, and compatibility posture |
| `id` | Yes | Stable curated-corpus experiment identifier |
| `public_case` | Yes | Public historical vulnerability identity and provenance |
| `repository` | Yes | Local repository input and revision references |
| `materialization` | Yes | Hardened local Git materialization policy |
| `reproducer` | Yes | Content-identified reproducer definition |
| `oracle` | Yes | Content-identified vulnerability-presence oracle |
| `comparison` | Yes | Equivalence contract and permitted differences |
| `environment` | Yes | Base environment contract |
| `setup` | Yes | Setup steps and explicit setup-network policy |
| `reproduction` | Yes | Reproduction steps and network policy |
| `limits` | Yes | CPU, memory, process, disk, output, and wall-clock limits |
| `evidence` | Yes | Required observations and artifacts |
| `limitations` | Yes | Known scope and interpretation limits |

## Public-case requirements

Every v0 case must:

- already be publicly disclosed;
- have recorded advisory provenance;
- have a curator and review record;
- identify why the baseline revision is considered known-vulnerable;
- avoid private or embargoed content; and
- remain subject to untrusted-execution controls.

Advisory metadata does not determine an Abaris observation.

## Repository requirements

v0 accepts only a local repository path supplied by the operator. The path is
resolved by the trusted source materializer and is not mounted directly into
the untrusted environment.

The experiment declares baseline and candidate Git references. Before
execution, both must resolve to immutable commit identities and isolated source
snapshot digests.

The working tree must not be modified.

## Materialization requirements

Source materialization reads only from the operator-supplied local repository
and writes separate Abaris-owned baseline and candidate trees.

The materialization policy must declare and enforce:

- non-interactive operation;
- ignored inherited Git configuration;
- empty Abaris-controlled hooks path;
- disabled clean, smudge, and process filters;
- disabled credential helpers and prompts;
- disabled LFS smudge and object downloads;
- disabled submodule initialization and recursion;
- disabled external protocols and remote helpers;
- rejected object alternates, promisor or partial-clone acquisition, replace
  refs, and other undeclared external object resolution;
- unsafe symlink rejection; and
- recorded commit, tree, snapshot, Git-version, and policy identities;
- recorded requested and enforced controls, rejected unsupported features,
  violations, and final materialization outcome; and
- refusal before execution when mandatory controls or identities cannot be
  verified.

Direct tree-object extraction with safety validation is preferred. A sanitized
checkout is only a possible bounded fallback. Remote source fetch, submodules,
and LFS content are unsupported by default in v0.

## Reproducer requirements

The same content-identified reproducer is applied to both revisions.

A reproducer declares bounded commands, fixtures, working directories, and
expected evidence sources. Command execution must use explicit argument arrays;
implicit shell strings are unsupported unless a future decision defines a
separate safe contract.

The reproducer does not declare whether the vulnerability is present.

## Oracle requirements

The same content-identified oracle and semantics are applied to both revisions.
The oracle defines the condition that constitutes vulnerability presence for
this experiment.

The oracle must support three outcomes:

- `PRESENT`
- `ABSENT`
- `INDETERMINATE`

The trusted core assigns the observation. Workload-created text, files, or exit
codes are untrusted oracle inputs and cannot directly set the observation.

Each run result separates:

- `observation`: `PRESENT`, `ABSENT`, or `INDETERMINATE`;
- `failure_reason`: a bounded technical category required only for
  `INDETERMINATE`; and
- attributable evidence consumed by trusted evaluation.

`failure_reason` is reserved for `INDETERMINATE`; determinate observations may
record warnings separately.

The derived paired conclusion is not a run-level failure category.

The exact oracle representation remains unresolved. Any accepted design must:

- be deterministic for the same valid evidence;
- make `ABSENT` distinguishable from evaluation failure;
- make required evidence explicit;
- use bounded evaluation;
- preserve evaluation inputs and output; and
- avoid AI dependency.

## Comparison-equivalence requirements

The paired experiment must use identical identities or semantics for:

- reproducer;
- oracle;
- declared fixtures;
- base environment contract;
- setup-network and reproduction-network policy;
- resource-limit policy;
- evidence requirements;
- trusted evaluator rules; and
- permitted differences.

The baseline and candidate source revisions intentionally differ. Revision-owned
setup inputs may differ, but the differences must be recorded and may make the
result `INDETERMINATE`.

## Setup requirements

Setup runs inside disposable isolation and may execute hostile project or
dependency behavior.

The setup-network policy is mandatory and explicit. The exact v0 policy values
remain unresolved, but the following rules apply:

- absence of a policy is invalid;
- access must never be inherited implicitly from the host;
- enabled access and relevant outcomes must be recorded;
- mutable or unresolved setup inputs may force `INDETERMINATE`; and
- setup access must not authorize reproduction network access.

## Reproduction requirements

The same reproducer is run against both prepared revisions.

For non-network cases, reproduction has no network. For service-target cases,
the experiment declares one target participant and a closed per-run network.

The closed network:

- attaches only the reproducer and declared target;
- allows reproducer-initiated connections only to declared target aliases and
  ports, plus response traffic;
- denies external egress, host and gateway access, cloud metadata, external
  DNS, undeclared peers, undeclared ingress, and published host ports;
- explicitly denies `169.254.0.0/16`, `169.254.169.254/32`,
  `fd00:ec2::254/128`, `fd20:ce::254/128`, `host.docker.internal`, and
  `gateway.docker.internal` where applicable;
- disables IPv6 unless explicitly modeled and equivalently restricted; and
- records or detects policy violations.

v0 must not probe third-party or production systems. The run is refused if the
backend cannot apply and verify the declared policy before execution. A policy
failure or violation discovered after execution begins makes the affected
observation `INDETERMINATE` and requires a bounded `failure_reason`.

## Resource limits

Every experiment declares bounded limits for:

- CPU;
- memory;
- process count;
- writable disk;
- retained artifact count and size;
- stdout and stderr size;
- structured evidence size and nesting;
- per-step wall-clock time; and
- total wall-clock time.

Limits are enforced outside workload authority. Limit events are evidence and
may cause `INDETERMINATE`.

## Evidence requirements

Required evidence includes:

- experiment contract identity, `private-draft` stability, compatibility
  posture, and specification digest;
- public-case provenance;
- exact baseline and candidate commit and snapshot digests;
- source-materialization policy, requested and enforced controls, tool versions,
  rejected features, violations, and outcome;
- reproducer and oracle digests;
- comparison-contract digest;
- environment and execution-backend identity;
- setup and reproduction network policies;
- resource-limit policy and violations;
- step status, exit status, signal, timeout, and termination reason;
- bounded stdout and stderr with truncation status;
- declared artifact provenance and digests;
- oracle evaluation inputs and result;
- a bounded `failure_reason` for every `INDETERMINATE` observation; and
- explicit omissions, contradictions, and unsupported conditions.

Every retained evidence object is content-addressed. Missing required evidence
prevents reliable `PRESENT` or `ABSENT` assignment.

## Observation and conclusion requirements

The result must always expose:

- baseline observation;
- candidate observation;
- a bounded failure reason for each `INDETERMINATE` observation;
- derived conclusion;
- evidence-bundle identity; and
- limitations.

The conclusion uses the matrix in
[architecture.md](architecture.md#conclusion-matrix). No additional conclusion
may be inferred from narrative advisory metadata.

`CANDIDATE_ONLY_PRESENT` is a blocking comparative conclusion. It does not
assert a regression or vulnerability introduction.

## Non-normative example

This example illustrates concepts and is not an accepted schema.

```json
{
  "schema": {
    "id": "dev.abaris.private-draft.experiment.v0",
    "stability": "private-draft",
    "compatibility": "none"
  },
  "id": "curated-public-example",
  "public_case": {
    "advisory_id": "PUBLIC-ADVISORY-ID",
    "provenance": "PUBLIC-ADVISORY-URL",
    "curation_status": "reviewed"
  },
  "repository": {
    "source": "local_path",
    "baseline_ref": "BASELINE_GIT_REF",
    "candidate_ref": "CANDIDATE_GIT_REF"
  },
  "materialization": {
    "policy": "hardened-local-tree-extraction",
    "hooks": "disabled",
    "filters": "disabled",
    "credential_helpers": "disabled",
    "lfs": "unsupported",
    "submodules": "unsupported",
    "alternates": "rejected",
    "replace_refs": "rejected",
    "partial_clone": "rejected",
    "repository_local_config": "ignored_or_rejected"
  },
  "reproducer": {
    "id": "reproducer-id",
    "digest": "sha256:REPRODUCER_DIGEST"
  },
  "oracle": {
    "id": "presence-oracle-id",
    "digest": "sha256:ORACLE_DIGEST",
    "outcomes": ["PRESENT", "ABSENT", "INDETERMINATE"]
  },
  "comparison": {
    "permitted_differences": ["source_revision"]
  },
  "environment": {
    "identity": "sha256:ENVIRONMENT_CONTRACT_DIGEST"
  },
  "setup": {
    "network_policy": "EXPLICIT_POLICY_REQUIRED"
  },
  "reproduction": {
    "network_policy": "closed-declared-target-only"
  },
  "limits": {
    "cpu": "BOUNDED_VALUE",
    "memory_bytes": "BOUNDED_VALUE",
    "processes": "BOUNDED_VALUE",
    "writable_disk_bytes": "BOUNDED_VALUE",
    "wall_clock_seconds": "BOUNDED_VALUE"
  },
  "evidence": {
    "required": ["step_outcomes", "oracle_inputs", "oracle_evaluation"]
  },
  "limitations": [
    "The conclusion applies only to this reproducer, oracle, and recorded conditions."
  ]
}
```

## Explicit v0 exclusions

The specification must not contain behavior for:

- private vulnerability inputs;
- community reproducer discovery or execution;
- vulnerability discovery or exploit generation;
- root-cause or variant analysis;
- affected-version inference or duplicate detection;
- automatic patch generation or disclosure;
- hosted execution;
- broad regression assertions; or
- AI-dependent observation or conclusion logic.

## Unresolved specification decisions

- Final serialization, schema, and compatibility policy.
- First supported target ecosystem.
- Allowed reproducer step forms.
- Oracle predicates, observation assignment, and bounded failure-reason
  taxonomy.
- Setup-network policy values and enforcement evidence.
- Dependency acquisition and identity model.
- Nondeterminism and rerun policy.
- Evidence retention, redaction, encryption, and signing.

These decisions must be informed by the representative-case stabilization gate,
not by designing a broad speculative schema.
