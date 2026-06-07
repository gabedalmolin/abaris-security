# Abaris Roadmap

This roadmap defines capability boundaries and exit criteria. It does not
commit to delivery dates or implementation dependencies.

## Phase 0: Architectural foundation

Objective: make the v0 claim, experiment, security boundary, and evidence model
precise before implementation.

Exit criteria:

- The paired experiment and equivalence requirements are documented.
- `PRESENT`, `ABSENT`, and `INDETERMINATE` have explicit semantics.
- The complete observation-to-conclusion matrix is documented.
- `REPRODUCER_BLOCKED` is explicitly limited to the recorded experiment.
- `CANDIDATE_ONLY_PRESENT` is defined as a blocking comparative asymmetry.
- Setup and reproduction network policies are separated.
- Networked reproduction uses a closed per-run reproducer-target network.
- Local Git materialization disables implicit execution and external
  acquisition behavior.
- Isolation, resource, secret, mount, and host-socket requirements are
  traceable to threats.
- Immutable input identities and content-addressed evidence are specified.
- Stable public schemas are gated on 3–5 reviewed representative cases.
- v0 exclusions are treated as enforceable product boundaries.
- Major unresolved decisions remain explicit.

## Next smallest milestone: executable specifications without execution

Objective: exercise private draft contracts against representative cases before
stabilizing any public schema or building a general runner.

Accepted first executable-specification increment:

- ADR-028 defines ordered fail-closed observation eligibility, one atomic
  declarative oracle predicate, and one primary bounded failure reason;
- the normative `private-draft` ADR-028 decision-table fixture covers every
  supported predicate's true and false outcomes, every bounded failure reason,
  precedence, invariants, and rejected oracle contracts; and
- the increment contains no runner, validator, production isolation backend, or
  public compatibility promise.

Remaining planned deliverables:

- additional private, disposable draft experiment and evidence contracts;
- table-driven fixtures for all nine observation pairs;
- invariant fixtures proving `CANDIDATE_ONLY_PRESENT` is never success and
  `INCONCLUSIVE` occurs only when at least one observation is `INDETERMINATE`;
- policy fixtures proving non-network cases cannot declare peers or ports,
  closed-network cases cannot publish host ports or use external egress, and
  materialization rejects unsupported features by default;
- a gate fixture refusing public stable schema status before representative-case
  review; and
- a threat-to-requirement acceptance matrix.

This milestone may use narrowly scoped prototypes to learn from 3–5 curated
historical cases, but it must not publish a stable schema or choose a production
isolation backend.

Representative cases must include:

- at least one networked target case;
- at least one CLI or file-input case;
- at least one process crash, exception, or signal case;
- at least one dependency or materialization-sensitive case;
- at least one fixed-candidate case;
- at least one failed or inconclusive case; and
- at least one candidate-only asymmetry.

## Phase 1: Minimal paired experiment

Objective: run one curated public historical case against one local baseline
revision and one local candidate revision.

Required capabilities:

- accept only local repository paths;
- resolve both revisions to immutable source identities;
- validate one reproducer and one oracle;
- apply the same comparison contract to both runs;
- create separate disposable isolated environments;
- expose no host secrets, home directory, or Docker socket;
- enforce explicit setup-network and reproduction-network policies;
- deny external reproduction egress;
- create a closed per-run reproducer-target network only for declared
  service-target cases;
- materialize Git source without inherited configuration, hooks, filters,
  credentials, LFS downloads, submodules, or external protocols;
- enforce CPU, memory, process, disk, output, and wall-clock limits;
- collect content-addressed evidence outside the workload trust boundary;
- emit paired observations before a derived conclusion; and
- require explicit human action for export.

Exit criteria:

- Every accepted run records immutable identities for source, reproducer,
  oracle, environment, and policy.
- Every accepted run produces one valid observation per revision.
- Every `INDETERMINATE` observation records a bounded failure reason distinct
  from the paired conclusion.
- All nine observation pairs map to the documented conclusion matrix.
- Missing or invalid required evidence produces `INDETERMINATE` and an
  `INCONCLUSIVE` paired result where applicable.
- Isolation conformance tests demonstrate no host secret, home mount, or Docker
  socket exposure.
- Resource-limit and network-policy violations are observed and preserved.
- The same accepted experiment produces stable observations across the
  documented supported environment, or nondeterminism is reported explicitly.

## Phase 2: Evidence quality and corpus discipline

Objective: make the bounded v0 corpus and result bundles independently
inspectable.

Planned capabilities:

- curated-corpus provenance and review records;
- content-addressed artifact bundle verification;
- explicit redaction, truncation, and omission metadata;
- deterministic rerun comparison;
- local evidence retention and deletion controls; and
- clear export previews.

Exit criteria:

- Every corpus case is public, reviewed, attributable, and reproducible under
  its declared supported conditions.
- Artifact tampering or missing content is detectable.
- A reviewer can reconstruct why each observation was assigned.
- Publication remains impossible without explicit human action.

## Later consideration, not v0 scope

Potential future work may include interoperability exports, additional target
ecosystems, or private workflows. Each requires a separate product decision,
threat-model update, and evidence-semantics review.

The following are not implied by this roadmap:

- vulnerability discovery;
- root-cause or variant verification;
- affected-version inference;
- broad regression validation;
- automatic patching or disclosure; or
- hosted execution.

## v0 success metrics

| Area | Target |
| --- | --- |
| Corpus governance | 100% curated, already-public historical cases with recorded provenance |
| Input identity | 100% of accepted runs record immutable source, reproducer, oracle, environment, and policy identities |
| Observation completeness | 100% of accepted paired runs produce explicit baseline and candidate observations |
| Failure classification | 100% of `INDETERMINATE` observations include a bounded failure reason |
| Conclusion determinism | 100% agreement with the documented nine-pair conclusion matrix |
| Fail-closed behavior | 100% of missing required evidence or unavailable mandatory controls avoid definitive `PRESENT` or `ABSENT` claims |
| Isolation conformance | No host secrets, home-directory mounts, or Docker socket visible in accepted execution |
| Policy evidence | Every run records setup-network, reproduction-network, and resource-limit policies and violations |
| Publication control | No evidence leaves local storage without an explicit human export action |

These metrics validate the supported v0 experiment, not general vulnerability
or patch correctness.

## Major unresolved decisions

- Which target ecosystem and project subset should v0 support first?
- Which isolation backend and host platforms can meet the mandatory profile?
- What exact setup-network policy modes are supported?
- How are setup dependencies acquired, pinned, cached, and attributed?
- What rerun policy is required before stable observations are accepted?
- What evidence is retained, redacted, encrypted, or excluded by default?
- What schema and compatibility policy should be published after the
  representative-case stabilization gate?
