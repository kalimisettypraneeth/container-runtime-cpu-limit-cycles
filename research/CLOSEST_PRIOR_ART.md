# Closest Prior Art — Container Runtime CPU-Limit Cycles

Review date: 2026-09-28  
Status: **EVIDENCE TABLE IN PROGRESS — NOVELTY UNVERIFIED**

Overlap taxonomy: `DIRECT`, `SUBSTANTIAL`, `PARTIAL`, `ADJACENT`, `FOUNDATIONAL`, `NONE IDENTIFIED`.

| Work | Year / venue | Research question or mechanism | Workload / setting | Metrics or decision signals | Artifact / primary source | Overlap | Exact differentiation still requiring proof |
|---|---|---|---|---|---|---|---|
| Linux CFS Bandwidth Control | kernel documentation | Enforce group CPU bandwidth using quota over a period; throttle after quota assignment is exhausted until replenishment | CFS task groups / cgroups | quota, period, throttled state and statistics | https://docs.kernel.org/scheduler/sched-bwc.html | FOUNDATIONAL | Period-aligned throttle/replenish is not a novel limit cycle. A contribution needs a distinct multi-period state trajectory or hysteresis. |
| CPU-Limits kill Performance: Time to rethink Resource Control | 2025 / ACM SoCC | Whether CPU limits are necessary and beneficial for latency-sensitive cloud-native applications | Cloud-native latency-sensitive applications | performance, resource waste, SLO violations, cost implications | DOI 10.1145/3772052.3772219; preprint https://arxiv.org/abs/2510.10747 | DIRECT | Broad CPU-limit harm is established. The remaining claim must be temporal regime characterization and control, not another harm demonstration. |
| HotSpot container awareness and `ActiveProcessorCount` | JDK 10+ / Oracle and OpenJDK documentation | Detect container CPU/memory limits and use an effective processor count for runtime ergonomics; allow an explicit CPU-count override | Java applications in Linux containers | active processor count used to size GC and ForkJoinPool-related thread pools | Oracle Java 21 command reference: https://docs.oracle.com/en/java/javase/21/docs/specs/man/java.html; OpenJDK JDK-8146115: https://bugs.openjdk.org/browse/JDK-8146115 | DIRECT | JVM quota awareness and processor-count-controlled runtime pools are established. Experiments must include default `UseContainerSupport`, explicit `ActiveProcessorCount`, GC configuration, and actual carrier/worker counts; a static runtime mismatch is not the claimed cycle. |
| Reducing Tail Latencies Through Environment- and Neighbour-aware Thread Management | 2024 / CoRR preprint (not peer reviewed) | Quantify OS-thread overcommitment under CPU quotas and neighbours; dynamically scale active worker threads from observed CPU usage | Rust/Go/Java and lightweight-thread runtimes on Linux cgroups | queue/worker latency, throughput, thread count, CPU usage; 10 ms controller interval | https://arxiv.org/abs/2407.11582 | SUBSTANTIAL | Quota-to-thread mismatch, latency/throughput tradeoffs, and dynamic worker-pool adaptation are already demonstrated. The paper must separate its multi-period quota/backlog regime from ordinary thread overcommitment and compare against active-thread control. |
| Currentcy: A Unifying Abstraction for Expressing Energy Management Policies | 2003 / USENIX ATC | Pace budget consumption within an epoch by delaying tasks that run ahead of schedule, reducing response-time variance relative to bursty epoch spending | ECOSystem/Linux resource containers and interactive applications | epoch progress, task budget, delay distribution and variance | https://www.usenix.org/legacy/publications/library/proceedings/usenix03/tech/full_papers/zeng/zeng_html/index.html | ADJACENT | Epoch self-pacing to smooth bursty budget consumption is established, although for energy rather than cgroup CPU quota. Novelty cannot rest on pacing alone; the controller needs cgroup-specific state, causal evidence, and modern baselines. |
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
- static quota/parallelism configurations, including HotSpot default container support and explicit `ActiveProcessorCount` sweeps;
- active-worker adaptation comparable to friendlypool;
- epoch self-pacing as an adjacent historical baseline where implementable;
- Autothrottle-style CPU throttle-ratio control;
- no-limit/request-only configurations where methodologically valid.

## Remaining searches before gate completion

- [ ] Explicit control-theoretic analyses of cgroup/CFS bandwidth dynamics.
- [ ] Backlog recovery and hysteresis under CPU quotas.
- [x] JVM container processor-count ergonomics and dynamic worker-pool overlap (Oracle/OpenJDK plus friendlypool preprint).
- [ ] JVM GC and carrier-thread interaction with quota timing, beyond static processor-count awareness.
- [ ] Forward/backward citation chains from CPU-Limits, Autothrottle, FIRM, and Sinan.
- [x] Historical epoch self-pacing that smooths bursty budget spending (Currentcy/ECOSystem).
- [ ] Modern cgroup-specific pacing controllers and artifact-compatible implementations.
- [ ] Artifact and workload compatibility for each mandatory baseline.

## Gate decision

**NOT COMPLETE.** This table rules out broad CPU-limit and adaptive-resource claims, but cgroup-specific control theory, JVM GC/carrier timing, modern pacing, hysteresis, and citation-chain searches remain open.
