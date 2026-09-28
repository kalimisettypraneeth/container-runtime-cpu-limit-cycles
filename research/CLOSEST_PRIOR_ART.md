# Closest Prior Art — Container Runtime CPU-Limit Cycles

Review date: 2026-09-28  
Status: **EVIDENCE TABLE IN PROGRESS — NOVELTY UNVERIFIED**

Overlap taxonomy: `DIRECT`, `SUBSTANTIAL`, `PARTIAL`, `ADJACENT`, `FOUNDATIONAL`, `NONE IDENTIFIED`.

| Work | Year / venue | Research question or mechanism | Workload / setting | Metrics or decision signals | Artifact / primary source | Overlap | Exact differentiation still requiring proof |
|---|---|---|---|---|---|---|---|
| Linux CFS Bandwidth Control | kernel documentation | Enforce group CPU bandwidth using quota over a period; throttle after quota assignment is exhausted until replenishment | CFS task groups / cgroups | quota, period, throttled state and statistics | https://docs.kernel.org/scheduler/sched-bwc.html | FOUNDATIONAL | Period-aligned throttle/replenish is not a novel limit cycle. A contribution needs a distinct multi-period state trajectory or hysteresis. |
| CPU-Limits kill Performance: Time to rethink Resource Control | 2025 / ACM SoCC | Whether CPU limits are necessary and beneficial for latency-sensitive cloud-native applications | Cloud-native latency-sensitive applications | performance, resource waste, SLO violations, cost implications | DOI 10.1145/3772052.3772219; preprint https://arxiv.org/abs/2510.10747 | DIRECT | Broad CPU-limit harm is established. The remaining claim must be temporal regime characterization and control, not another harm demonstration. |
| Container-aware GOMAXPROCS | 2025 / Go 1.25 official runtime behavior | Align Go runtime parallelism with cgroup CPU bandwidth limits | Go programs in Linux containers | GOMAXPROCS, quota/period throughput, throttling, tail latency | https://go.dev/blog/container-aware-gomaxprocs and https://go.dev/doc/go1.25 | DIRECT | Runtime/limit mismatch and quota exhaustion are established. The paper must compare against Go 1.25 defaults and isolate any multi-period regime. |
| Uber automaxprocs | public engineering artifact | Set GOMAXPROCS from Linux container CPU quota | Go services under container quotas | p50/p99, RPS, throttling counters in published package documentation | https://pkg.go.dev/go.uber.org/automaxprocs | DIRECT | Matching parallelism to quota is established engineering practice and a required baseline. |
| Autothrottle: A Practical Bi-Level Approach to Resource Management for SLO-Targeted Microservices | 2024 / USENIX NSDI | Translate application SLO feedback into per-service CPU throttle-ratio targets | Three microservice applications with production workload traces | end-to-end latency/SLO, CPU savings, service throttle ratios | Paper: https://www.usenix.org/conference/nsdi24/presentation/wang-zibo; code: https://github.com/microsoft/autothrottle | DIRECT | CPU throttle ratios as feedback signals and controlled targets are established. A damping controller must distinguish the proposed dynamic regime and compare with bi-level control. |
| FIRM: An Intelligent Fine-grained Resource Management Framework for SLO-Oriented Microservices | 2020 / USENIX OSDI | Identify critical microservices and adjust fine-grained resources to meet SLOs efficiently | Four microservice benchmarks | SLO violations and requested CPU limits, among resource-management metrics | https://www.usenix.org/conference/osdi20/presentation/qiu | SUBSTANTIAL | Fine-grained CPU/SLO resource control is established; novelty cannot rest on dynamically tuning CPU limits alone. |
| Sinan: ML-Based and QoS-Aware Resource Management for Cloud Microservices | 2021 / ACM ASPLOS | Use data-driven models to allocate resources across dependent microservices while meeting QoS | Cloud microservice applications with tier dependencies | end-to-end tail latency/QoS and resource allocation | Author paper: https://people.csail.mit.edu/delimitrou/papers/2021.asplos.sinan.pdf; code leads: https://github.com/zyqCSL/sinan-gcp | SUBSTANTIAL | Dependency-aware, data-driven CPU allocation is established. A new claim must target quota/runtime temporal dynamics, not generic QoS-aware allocation. |

## Operational discriminator

A quota waveform is expected kernel behavior: within each configured period, quota is consumed and runnable tasks may be throttled until replenishment.

A claimed **multi-period limit cycle or hysteresis** must satisfy all of the following:

1. a state vector is declared, including at least backlog, latency, throttle time/fraction, achieved service rate, and runtime parallelism;
2. a repeatable trajectory spans multiple quota periods rather than merely repeating the configured kernel period;
3. increasing and decreasing load sweeps test for distinct transition thresholds or recovery paths;
4. phase/state persists under controlled repetition and is not explained solely by arrival-phase alignment;
5. the phenomenon remains after comparison with container-aware runtime defaults and established CPU/SLO controllers.

## Current synthesis

Broad claims about CPU-limit harm, throttling, runtime mismatch, throttle-ratio feedback, or adaptive CPU allocation are unsafe. The viable candidate is narrower: demonstrate and operationally distinguish a persistent multi-period dynamic regime caused by quota, backlog, burstiness, and runtime scheduling, then show that a controller damps it better than established baselines.

Mandatory baselines now include:

- ordinary CFS quota behavior;
- Go 1.25 container-aware GOMAXPROCS or runtime-equivalent settings;
- Uber automaxprocs where applicable;
- static quota/parallelism configurations;
- Autothrottle-style CPU throttle-ratio control;
- no-limit/request-only configurations where methodologically valid.

## Remaining searches before gate completion

- [ ] Explicit control-theoretic analyses of cgroup/CFS bandwidth dynamics.
- [ ] Backlog recovery and hysteresis under CPU quotas.
- [ ] JVM container scheduling, GC, and carrier-thread interaction with quotas.
- [ ] Forward/backward citation chains from CPU-Limits, Autothrottle, FIRM, and Sinan.
- [ ] Comparable controllers using pacing rather than quota resizing.
- [ ] Artifact and workload compatibility for each mandatory baseline.

## Gate decision

**NOT COMPLETE.** This table rules out broad CPU-limit and adaptive-resource claims, but control-theory, JVM/runtime, hysteresis, and citation-chain searches remain open.
