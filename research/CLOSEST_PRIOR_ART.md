# Closest Prior Art — Container Runtime CPU-Limit Cycles

Review date: 2026-09-29  
Status: **EVIDENCE TABLE IN PROGRESS — NOVELTY UNVERIFIED**

Overlap taxonomy: `DIRECT`, `SUBSTANTIAL`, `PARTIAL`, `ADJACENT`, `FOUNDATIONAL`, `NONE IDENTIFIED`.

| Work | Year / venue | Research question or mechanism | Workload / setting | Metrics or decision signals | Artifact / primary source | Overlap | Exact differentiation still requiring proof |
|---|---|---|---|---|---|---|---|
| Linux CFS Bandwidth Control | kernel documentation | Enforce group CPU bandwidth using quota over a period; throttle after quota assignment is exhausted until replenishment | CFS task groups / cgroups | quota, period, throttled state and statistics | https://docs.kernel.org/scheduler/sched-bwc.html | FOUNDATIONAL | Period-aligned throttle/replenish is not a novel limit cycle. A contribution needs a distinct multi-period state trajectory or hysteresis. |
| Constant Bandwidth Server and Linux SCHED_DEADLINE | 1998 onward / real-time scheduling and Linux | Reserve CPU using runtime, deadline, and period parameters; replenish depleted runtime and preserve temporal isolation for variable execution demand | Real-time tasks and Linux deadline scheduling | remaining runtime, replenishment time, deadline, period, admission bandwidth | Linux kernel documentation: https://docs.kernel.org/scheduler/sched-deadline.html | FOUNDATIONAL | Periodic budget reservation, depletion, and replenishment are established. The candidate must not relabel a periodic server as a cgroup limit cycle; it must identify CFS/cgroup-specific state and predictions that CBS does not explain. |
| Feedback Control Real-Time Scheduling: Framework, Modeling, and Algorithms | 2002 / Real-Time Systems; framework introduced at ECRTS 1999 | Model real-time scheduling as a dynamic system and use feedback to control CPU utilization and QoS under uncertain, time-varying workloads | Soft real-time systems with variable execution demand | utilization, deadline-miss behavior, transient and steady-state response | DOI 10.1023/A:1015398403337; repository record https://doi.org/10.18130/V3S31K | SUBSTANTIAL | Closed-loop CPU scheduling and transient/steady-state controller design are established. Novelty requires a cgroup-specific nonlinear or multi-period mechanism, operational falsifiers, and comparison with an FCS-style controller rather than generic feedback-control claims. |
| CPU-Limits kill Performance: Time to rethink Resource Control | 2025 / ACM SoCC | Whether CPU limits are necessary and beneficial for latency-sensitive cloud-native applications | Cloud-native latency-sensitive applications | performance, resource waste, SLO violations, cost implications | DOI 10.1145/3772052.3772219; preprint https://arxiv.org/abs/2510.10747 | DIRECT | Broad CPU-limit harm is established. The remaining claim must be temporal regime characterization and control, not another harm demonstration. |
| HotSpot container awareness and `ActiveProcessorCount` | JDK 10+ / Oracle and OpenJDK documentation | Detect container CPU/memory limits and use an effective processor count for runtime ergonomics; allow an explicit CPU-count override | Java applications in Linux containers | active processor count used to size GC and ForkJoinPool-related thread pools | Oracle Java 21 command reference: https://docs.oracle.com/en/java/javase/21/docs/specs/man/java.html; OpenJDK JDK-8146115: https://bugs.openjdk.org/browse/JDK-8146115 | DIRECT | JVM quota awareness and processor-count-controlled runtime pools are established. Experiments must include default `UseContainerSupport`, explicit `ActiveProcessorCount`, GC configuration, and actual carrier/worker counts; a static runtime mismatch is not the claimed cycle. |
| Reducing Tail Latencies Through Environment- and Neighbour-aware Thread Management | 2024 / CoRR preprint (not peer reviewed) | Quantify OS-thread overcommitment under CPU quotas and neighbours; dynamically scale active worker threads from observed CPU usage | Rust/Go/Java and lightweight-thread runtimes on Linux cgroups | queue/worker latency, throughput, thread count, CPU usage; 10 ms controller interval | https://arxiv.org/abs/2407.11582 | SUBSTANTIAL | Quota-to-thread mismatch, latency/throughput tradeoffs, and dynamic worker-pool adaptation are already demonstrated. The paper must separate its multi-period quota/backlog regime from ordinary thread overcommitment and compare against active-thread control. |
| Currentcy: A Unifying Abstraction for Expressing Energy Management Policies | 2003 / USENIX ATC | Pace budget consumption within an epoch by delaying tasks that run ahead of schedule, reducing response-time variance relative to bursty epoch spending | ECOSystem/Linux resource containers and interactive applications | epoch progress, task budget, delay distribution and variance | https://www.usenix.org/legacy/publications/library/proceedings/usenix03/tech/full_papers/zeng/zeng_html/index.html | ADJACENT | Epoch self-pacing to smooth bursty budget consumption is established, although for energy rather than cgroup CPU quota. Novelty cannot rest on pacing alone; the controller needs cgroup-specific state, causal evidence, and modern baselines. |
| Container-aware GOMAXPROCS | 2025 / Go 1.25 official runtime behavior | Align Go runtime parallelism with cgroup CPU bandwidth limits | Go programs in Linux containers | GOMAXPROCS, quota/period throughput, throttling, tail latency | https://go.dev/blog/container-aware-gomaxprocs and https://go.dev/doc/go1.25 | DIRECT | Runtime/limit mismatch and quota exhaustion are established. The paper must compare against Go 1.25 defaults and isolate any multi-period regime. |
| Uber automaxprocs | public engineering artifact | Set GOMAXPROCS from Linux container CPU quota | Go services under container quotas | p50/p99, RPS, throttling counters in published package documentation | https://pkg.go.dev/go.uber.org/automaxprocs | DIRECT | Matching parallelism to quota is established engineering practice and a required baseline. |
| Autothrottle: A Practical Bi-Level Approach to Resource Management for SLO-Targeted Microservices | 2024 / USENIX NSDI | Translate application SLO feedback into per-service CPU throttle-ratio targets | Three microservice applications with production workload traces | end-to-end latency/SLO, CPU savings, service throttle ratios | Paper: https://www.usenix.org/conference/nsdi24/presentation/wang-zibo; code: https://github.com/microsoft/autothrottle | DIRECT | CPU throttle ratios as feedback signals and controlled targets are established. A damping controller must distinguish the proposed dynamic regime and compare with bi-level control. |
| FIRM: An Intelligent Fine-grained Resource Management Framework for SLO-Oriented Microservices | 2020 / USENIX OSDI | Identify critical microservices and adjust fine-grained resources to meet SLOs efficiently | Four microservice benchmarks | SLO violations and requested CPU limits, among resource-management metrics | https://www.usenix.org/conference/osdi20/presentation/qiu | SUBSTANTIAL | Fine-grained CPU/SLO resource control is established; novelty cannot rest on dynamically tuning CPU limits alone. |
| Sinan: ML-Based and QoS-Aware Resource Management for Cloud Microservices | 2021 / ACM ASPLOS | Use data-driven models to allocate resources across dependent microservices while meeting QoS | Cloud microservice applications with tier dependencies | end-to-end tail latency/QoS and resource allocation | Author paper: https://people.csail.mit.edu/delimitrou/papers/2021.asplos.sinan.pdf; code leads: https://github.com/zyqCSL/sinan-gcp | SUBSTANTIAL | Dependency-aware, data-driven CPU allocation is established. A new claim must target quota/runtime temporal dynamics, not generic QoS-aware allocation. |
| Cilantro: Performance-Aware Resource Allocation for General Objectives via Online Feedback | 2023 / USENIX OSDI | Learn performance/resource mappings online and allocate resources for general objectives | Cluster and microservice workloads | utility/performance feedback and allocation | https://www.usenix.org/conference/osdi23/presentation/bhardwaj | SUBSTANTIAL | Online feedback allocation is established. CPU-Limits identifies that Cilantro's model assumes allocation bounds; the candidate cannot claim generic performance-feedback allocation. |
| Ursa: Analytically-Driven Resource Management for Cloud-Native Microservices | 2024 / arXiv preprint | Decompose end-to-end SLA into per-service targets and resource allocations with lightweight analytical models | Social network, media, and video-processing microservices | SLA violations, CPU allocation, control-plane/data-collection cost | https://arxiv.org/abs/2401.02920 | SUBSTANTIAL | Ursa explicitly compares with Sinan and FIRM. Analytical SLA decomposition and rapid per-service resource exploration are established; they do not establish the claimed CFS multi-period regime. |
| OpenJDK cgroup CPU-count semantics | 2021 onward / OpenJDK runtime records | Derive effective processor count from cgroup quota/shares and use it to size GC, compiler, and ForkJoin-related pools | HotSpot in cgroup environments | effective CPU count and derived runtime-pool sizing | OpenJDK review record: https://mail.openjdk.org/pipermail/hotspot-runtime-dev/2021-March/046629.html; Oracle `ActiveProcessorCount` documentation | DIRECT | Quota-aware JVM sizing and its ambiguity under elastic request/limit configurations are established. A temporal claim must measure GC/compiler/worker/carrier activity relative to quota periods; static sizing evidence is insufficient. |

## Operational discriminator

A quota waveform is expected kernel behavior: within each configured period, quota is consumed and runnable tasks may be throttled until replenishment.

A claimed **multi-period limit cycle or hysteresis** must satisfy all of the following:

1. a state vector is declared, including at least backlog, latency, throttle time/fraction, achieved service rate, and runtime parallelism;
2. a repeatable trajectory spans multiple quota periods rather than merely repeating the configured kernel period;
3. increasing and decreasing load sweeps test for distinct transition thresholds or recovery paths;
4. phase/state persists under controlled repetition and is not explained solely by arrival-phase alignment;
5. the phenomenon remains after comparison with container-aware runtime defaults and established CPU/SLO controllers.

## Current synthesis

Broad claims about CPU-limit harm, throttling, runtime mismatch, periodic budget depletion/replenishment, generic feedback scheduling, throttle-ratio feedback, or adaptive CPU allocation are unsafe. CBS/SCHED_DEADLINE provides the periodic-server null model, while feedback-control real-time scheduling establishes closed-loop CPU/QoS regulation. The viable candidate is narrower: demonstrate and operationally distinguish a persistent CFS/cgroup-specific multi-period regime caused by quota, backlog, burstiness, and runtime scheduling that those established models do not predict, then show that a controller damps it better than established baselines.

Mandatory baselines now include:

- ordinary CFS quota behavior;
- a CBS/SCHED_DEADLINE-inspired periodic-budget null model, with explicit runtime/period/replenishment predictions;
- an FCS-style utilization/QoS feedback controller or a documented reason it cannot be implemented comparably;
- Go 1.25 container-aware GOMAXPROCS or runtime-equivalent settings;
- Uber automaxprocs where applicable;
- static quota/parallelism configurations, including HotSpot default container support and explicit `ActiveProcessorCount` sweeps;
- active-worker adaptation comparable to friendlypool;
- epoch self-pacing as an adjacent historical baseline where implementable;
- Autothrottle-style CPU throttle-ratio control;
- no-limit/request-only configurations where methodologically valid.

## Bounded chain and mechanism-search result

Backward chaining from CPU-Limits identifies FIRM, Cilantro, Autothrottle, Ursa, SHOWAR, Erlang, and autoscaling work as CPU-limit-dependent resource-management predecessors. A forward/adjacent chain through Ursa links Sinan and FIRM as representative learned resource managers. Targeted primary-source and official-runtime searches found processor-count/runtime-pool coupling, one-period CFS/CBS budget semantics, burst accumulation, worker adaptation, and feedback allocation. They did **not identify** a primary source that directly models the proposed CFS/cgroup-specific multi-period backlog hysteresis or a HotSpot GC/carrier-to-quota phase loop. This is a bounded search result, not proof that no such work exists and not evidence of novelty.

## Remaining searches before gate completion

- [x] Foundational periodic CPU-budget/resource-reservation theory and Linux CBS implementation semantics.
- [x] General feedback-control real-time scheduling and dynamic CPU-utilization control.
- [x] Bounded cgroup/CFS-bandwidth-specific control, backlog-recovery, and hysteresis search completed; no direct primary-source mechanism match identified, without treating that negative result as proof.
- [x] Backlog-recovery and up/down-sweep literature search reconciled into explicit falsifiers; empirical evidence remains future experiment work.
- [x] JVM container processor-count ergonomics and dynamic worker-pool overlap (Oracle/OpenJDK plus friendlypool preprint).
- [x] Official JVM GC/compiler/ForkJoin sizing interaction with cgroup-derived processor count reviewed; no direct primary evidence for a quota-phase GC/carrier loop identified, so that mechanism remains an experiment hypothesis.
- [x] Backward/adjacent citation chains from CPU-Limits, Autothrottle, FIRM, Sinan, Cilantro, and Ursa reconciled; bounded forward searching is logged and does not establish exhaustiveness.
- [x] Historical epoch self-pacing that smooths bursty budget spending (Currentcy/ECOSystem).
- [x] Modern cgroup pacing/burst search reconciled: kernel burst/slice semantics and Autothrottle-style control are established, but no maintained directly compatible cgroup-phase pacing artifact was verified.
- [x] Public artifact availability, initial license, and environment constraints inventoried in `research/BASELINE_ARTIFACTS.md`.
- [ ] Build, smoke, behavioral-conformance, and workload-compatibility verification for selected executable baselines.

## Gate decision

**NOT COMPLETE.** The bounded mechanism and citation-chain searches are now reconciled and narrow the hypothesis without proving novelty. The gate remains open because selected baselines have not yet passed fresh zero-cost build, smoke, behavioral-conformance, and common-workload checks. No experiment-design or implementation work may start until that executable-baseline evidence is committed and read back.
