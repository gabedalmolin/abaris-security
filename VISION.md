# Abaris Vision

## Purpose

Abaris should help maintainers answer a narrow, practical question:

> Did the same supplied reproducer demonstrate the same explicitly defined
> vulnerability-presence oracle on a known-vulnerable baseline revision and on
> a candidate revision under recorded conditions?

Abaris preserves the evidence needed to inspect that paired experiment. It does
not infer more than the experiment demonstrates.

## Product position

Abaris is a maintainer-controlled patch-behavior validation and
evidence-preservation harness.

It is not a universal vulnerability verifier. It does not prove root cause,
patch completeness, absence of variants, absence of regressions, or general
software security.

The initial product deliberately favors a small defensible claim over a broad
but unsupported one.

## Core model

### Observations are primary

Each revision produces one explicit three-state oracle observation:

- `PRESENT`
- `ABSENT`
- `INDETERMINATE`

The observation must be traceable to immutable inputs, trusted evaluation, and
content-addressed evidence.

### Conclusions are derived

Conclusions summarize the paired observations:

- `REPRODUCER_BLOCKED`
- `REPRODUCER_STILL_SUCCEEDS`
- `BASELINE_NOT_REPRODUCED`
- `CANDIDATE_ONLY_PRESENT`
- `INCONCLUSIVE`

Conclusions never replace or obscure the underlying observation pair.
`CANDIDATE_ONLY_PRESENT` records a valid candidate-only asymmetry. It is not a
verified regression, vulnerability, severity, or root-cause claim.

### Evidence limits the claim

Abaris's strongest conclusion is scoped to:

- the exact baseline and candidate source identities;
- the exact reproducer and oracle identities;
- the recorded setup and reproduction policy;
- the recorded isolated environments;
- the collected evidence; and
- the observed run conditions.

## Principles

### Maintainer control

Core execution and evidence storage must be self-hostable and controlled by the
maintainer or authorized operator. No hosted service is required for v0.

### Curated inputs without misplaced trust

v0 uses only curated, already-public historical vulnerability cases. Curation
reduces governance and disclosure risk; it does not make repositories,
reproducers, setup steps, or artifacts safe to execute.

### Paired experimental discipline

The same reproducer, oracle, and comparison policy must be applied to both
revisions. Any permitted difference must be explicit, recorded, and treated as
a limitation of the comparison.

### Explicit uncertainty

`INDETERMINATE` is a first-class observation. Missing evidence, timeouts,
resource exhaustion, unsupported behavior, oracle ambiguity, or isolation
failure must not be converted into `ABSENT`.

### Secure disposable execution

Untrusted execution must not receive host secrets, home-directory mounts, or a
Docker or equivalent host-control socket. It must run with bounded resources
and explicit network policy in disposable isolation.

For cases requiring a network service, reproduction uses a closed per-run
network containing only declared reproducer and target participants. External
egress, host access, metadata access, undeclared peers, undeclared ingress, and
published host ports remain denied.

### Evidence before schema stability

Private draft contracts may evolve while representative cases are executed.
Abaris must not publish or promise a stable schema until 3–5 representative
historical cases have demonstrated the necessary experiment, oracle, network,
materialization, and evidence shapes.

### Deterministic trusted core

The core that validates inputs, evaluates the oracle, records observations, and
derives conclusions must not depend on AI. Future optional assistance must
remain outside that core and must never authoritatively set an observation or
conclusion.

### Human-controlled publication

Evidence and conclusions remain local until a human explicitly exports or
publishes them. Abaris does not automate disclosure.

### Unsupported claims are defects

Output language must not imply universal remediation, root-cause validation,
affected-version coverage, variant coverage, or broad regression assurance.

## v0 exclusions

v0 excludes:

- private vulnerability reports;
- untrusted community reproducer intake;
- vulnerability discovery;
- exploit generation;
- root-cause verification;
- variant analysis;
- affected-version inference;
- duplicate detection;
- automatic patch generation;
- automatic disclosure;
- hosted execution; and
- broad regression claims.

These exclusions are product boundaries, not a backlog promise.

## Definition of v0 success

An authorized maintainer can run a curated public historical reproducer against
a local baseline and candidate revision, receive explicit observations for both
runs, inspect a conservative derived conclusion, and independently trace the
result to content-addressed evidence and immutable input identities.

The system must be equally reliable at refusing to conclude when the experiment
is invalid or indeterminate.
