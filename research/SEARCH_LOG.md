# Prior-Art Search Log

| Date | Database/Search Engine | Query | Filters | Number/Type of Results | Relevant Results |
|---|---|---|---|---|---|
| 2026-09-26 | Kubernetes documentation | CPU limits throttling cgroups | official documentation | documentation | CPU limits are kernel/cgroup-enforced; throttling itself is not novel. |
| 2026-09-27 | ACM SoCC / web | "CPU-Limits kill Performance" | SoCC 2025 / primary or author-hosted evidence | conference paper | Shetty et al., SoCC 2025, DOI 10.1145/3772052.3772219, establishes broad CPU-limit performance/SLO harm. |
| 2026-09-27 | Go official documentation | Go 1.25 GOMAXPROCS container CPU limit | go.dev only | release notes + runtime article | Go 1.25 makes GOMAXPROCS container-aware and documents quota exhaustion, throttling, and tail-latency interaction. |
| 2026-09-28 | Linux kernel documentation | CFS bandwidth quota period throttled replenishment | official kernel source | scheduler documentation | Linux defines quota within a period and throttling after exhaustion until replenishment. This basic periodic pattern is established behavior, not a novel limit cycle. |
| 2026-09-28 | Mechanism-gap review | cgroup CPU quota limit cycle hysteresis backlog burst recovery | exact mechanism + synonyms | research and engineering leads | Candidate novelty requires a multi-period dynamic signature or hysteresis distinguishable from ordinary quota-period throttling; no originality conclusion is recorded. |
| 2026-09-28 | Runtime/controller review | JVM Go runtime CPU quota pacing feedback controller throttling | runtime + control terms | primary docs and research leads | Go already adapts GOMAXPROCS to limits. Any controller must be compared with container-aware runtime defaults and fixed pacing/admission baselines. |

## Evidence discipline

- Kernel documentation defines mechanism semantics.
- Peer-reviewed work establishes performance overlap.
- Runtime documentation constrains claims about container awareness.
- The proposed multi-period limit-cycle/hysteresis regime and damping controller remain **UNVERIFIED** until explicit scholarly and citation-chain searches are complete.
