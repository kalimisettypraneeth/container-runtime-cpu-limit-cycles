# Prior-Art Search Log

| Date | Database/Search Engine | Query | Filters | Number/Type of Results | Relevant Results |
|---|---|---|---|---|---|
| 2026-09-26 | Kubernetes documentation | CPU limits throttling cgroups | official documentation | documentation | CPU limits are kernel/cgroup-enforced; throttling itself is not novel. |
| 2026-09-27 | ACM SoCC / web | "CPU-Limits kill Performance" | SoCC 2025 / primary or author-hosted evidence | conference paper | Shetty et al., SoCC 2025, DOI 10.1145/3772052.3772219, establishes broad CPU-limit performance/SLO harm. |
| 2026-09-27 | Go official documentation | Go 1.25 GOMAXPROCS container CPU limit | go.dev only | release notes + runtime article | Go 1.25 makes GOMAXPROCS container-aware and documents quota exhaustion, throttling, and tail-latency interaction. |
| 2026-09-28 | Linux kernel documentation | CFS bandwidth quota period throttled replenishment | official kernel source | scheduler documentation | Linux defines quota within a period and throttling after exhaustion until replenishment. This basic periodic pattern is established behavior, not a novel limit cycle. |
| 2026-09-28 | Mechanism-gap review | cgroup CPU quota limit cycle hysteresis backlog burst recovery | exact mechanism + synonyms | research and engineering leads | Candidate novelty requires a multi-period dynamic signature or hysteresis distinguishable from ordinary quota-period throttling; no originality conclusion is recorded. |
| 2026-09-28 | Runtime/controller review | JVM Go runtime CPU quota pacing feedback controller throttling | runtime + control terms | primary docs and research leads | Go already adapts GOMAXPROCS to limits. Any controller must be compared with container-aware runtime defaults and fixed pacing/admission baselines. |
| 2026-09-28 | Go first-party and package evidence | container-aware GOMAXPROCS automaxprocs quota p99 throttling | official runtime + established package | runtime article, release notes, package documentation | Go 1.25 and Uber automaxprocs already align parallelism to quota and document throttling/tail-latency effects. |
| 2026-09-28 | USENIX NSDI | Autothrottle CPU throttle ratio SLO microservices | peer-reviewed primary source + artifact | NSDI 2024 paper and Microsoft code repository | Autothrottle uses application feedback to set per-service CPU throttle-ratio targets; throttle feedback/control is direct prior art. |
| 2026-09-28 | USENIX OSDI | FIRM resource management CPU limit SLO microservices | peer-reviewed primary source | OSDI 2020 paper | FIRM performs fine-grained resource management and reduces SLO violations while lowering requested CPU limits. |
| 2026-09-28 | ACM ASPLOS / author paper | Sinan QoS aware resource management microservices CPU | peer-reviewed author-hosted primary paper | ASPLOS 2021 paper + code leads | Sinan establishes data-driven, dependency-aware resource allocation for microservices and end-to-end QoS. |
| 2026-09-28 | Mechanism synthesis | CPU quota multi-period state trajectory hysteresis arrival phase | operational discriminator | evidence synthesis | A limit-cycle claim now requires multi-period state, controlled up/down sweeps, and separation from arrival-phase alignment and ordinary CFS replenishment. |

## Evidence discipline

- Kernel documentation defines mechanism semantics.
- Peer-reviewed work establishes CPU-limit harm and adaptive resource-management overlap.
- Runtime/package documentation constrains claims about container awareness and parallelism matching.
- Broad claims about CPU throttling, CPU-limit harm, throttle-ratio feedback, or adaptive CPU allocation are rejected.
- The proposed multi-period limit-cycle/hysteresis regime and damping controller remain **UNVERIFIED** pending control-theory, JVM/runtime, pacing-controller, and citation-chain searches.
