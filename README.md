# Abaris

Abaris is a maintainer-controlled patch-behavior validation and
evidence-preservation harness.

Given a local repository, a known-vulnerable baseline revision, a candidate
revision, one supplied reproducer, and one vulnerability-presence oracle,
Abaris is intended to run the same paired experiment against both revisions in
isolated environments and preserve the observed evidence.

Abaris does not claim that a vulnerability is universally fixed. Its strongest
defensible conclusion is:

> The supplied reproducer demonstrated the defined vulnerability-presence
> oracle on the baseline revision and did not demonstrate it on the candidate
> revision under the recorded conditions.

## Project status

Abaris is in a documentation and architectural-foundation phase. There is no
working CLI or supported execution backend. Security controls described in this
repository are requirements, not implemented guarantees.

## v0 experiment

The v0 workflow accepts:

- a local repository path;
- a known-vulnerable baseline Git revision;
- a candidate Git revision;
- one curated reproducer for an already-public historical vulnerability;
- one explicitly defined vulnerability-presence oracle; and
- an explicit execution policy.

The same reproducer, oracle, and comparison policy are applied to both
revisions. Each run produces one primary observation:

| Observation | Meaning |
| --- | --- |
| `PRESENT` | The oracle was evaluated successfully and demonstrated the defined vulnerability-presence condition. |
| `ABSENT` | The oracle was evaluated successfully and did not demonstrate the defined vulnerability-presence condition. |
| `INDETERMINATE` | The oracle could not be evaluated reliably under the recorded conditions. |

Every `INDETERMINATE` observation must include a bounded technical
`failure_reason` separate from the paired conclusion.

The paired observations produce one conservative derived conclusion:

| Conclusion | Required observation pair |
| --- | --- |
| `REPRODUCER_BLOCKED` | Baseline `PRESENT`, candidate `ABSENT` |
| `REPRODUCER_STILL_SUCCEEDS` | Baseline `PRESENT`, candidate `PRESENT` |
| `BASELINE_NOT_REPRODUCED` | Baseline `ABSENT`, candidate `ABSENT` |
| `CANDIDATE_ONLY_PRESENT` | Baseline `ABSENT`, candidate `PRESENT` |
| `INCONCLUSIVE` | Any pair containing `INDETERMINATE` |

`REPRODUCER_BLOCKED` does not mean universally fixed. The paired observations,
immutable input identities, execution conditions, and evidence remain the
authoritative result. `CANDIDATE_ONLY_PRESENT` is a blocking comparative
asymmetry, not proof that the candidate introduced a vulnerability or
regression.

## Safety requirements

Repository contents, revisions, setup steps, reproducers, and workload output
must be treated as untrusted executable content, including when they come from
a curated public corpus.

The future supported execution profile must:

- expose no host secrets;
- expose no home-directory mounts;
- expose no Docker or equivalent host-control socket;
- use disposable isolated environments;
- deny external reproduction egress;
- for service-target cases, permit only declared reproducer-to-target
  communication on a closed per-run network;
- apply an explicit setup-network policy;
- enforce CPU, memory, process, disk, output, and wall-clock limits; and
- fail closed when required controls or evidence are unavailable.

Containers alone must not be described as a sufficient hostile-code boundary.

## v0 scope

Abaris v0 is:

- maintainer-controlled and self-hostable;
- limited to local repository paths;
- limited to curated, already-public historical vulnerability cases;
- deterministic at its trusted observation and conclusion core;
- evidence-first and conservative; and
- human-controlled for any publication or export.

Abaris v0 explicitly excludes:

- private vulnerability reports;
- untrusted community reproducers;
- vulnerability discovery or exploit generation;
- root-cause verification or variant analysis;
- affected-version inference or duplicate detection;
- automatic patch generation or disclosure;
- hosted execution;
- broad regression claims; and
- any AI dependency in the deterministic core.

v0 source materialization must treat Git as a security boundary. It reads only
from the operator-supplied local repository, ignores inherited Git
configuration, and disables hooks, filters, credential helpers, LFS downloads,
submodule initialization, and external protocols by default.

v0 may use private, disposable draft contracts while validating representative
cases. It must not publish or promise a stable schema before 3–5 representative
historical cases have completed review. Every draft artifact must self-identify
as `private-draft` with no compatibility guarantee.

## Documentation

- [Vision](VISION.md)
- [Roadmap](ROADMAP.md)
- [Threat model](THREAT_MODEL.md)
- [Security policy](SECURITY.md)
- [Contributing](CONTRIBUTING.md)
- [Architecture](docs/architecture.md)
- [Reproducer specification](docs/reproducer-specification.md)
- [Decision log](docs/decision-log.md)

## Standards and interoperability

Abaris should reuse established formats where their semantics fit, without
allowing advisory or scanner metadata to determine observations. Potential
future integrations include OSV for public vulnerability identity and affected
range context, SARIF for compatible result export, VEX for carefully scoped
statements, and public OSS-Fuzz testcase metadata.

Those integrations are not part of v0 unless separately accepted. Abaris needs
its own narrow evidence model because existing formats do not fully express the
paired experiment and its immutable execution conditions.

## Responsible use

Abaris must only be used against repositories and environments the operator is
authorized to test. It must not test third-party or production systems and must
never publish vulnerability information automatically.

See [SECURITY.md](SECURITY.md) before attempting implementation or execution.
