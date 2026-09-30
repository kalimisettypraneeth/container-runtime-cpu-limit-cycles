# Differentiation — Container Runtime CPU-Limit Cycles

Review date: 2026-09-29  
Reconciled evidence head: `dc0e2045b333cbb417c9e065efdf6236354db2be`  
Status: **PROVISIONAL — NOVELTY UNVERIFIED**

## Research question

Do interactions among cgroup CPU bandwidth, bursty demand, backlog, and managed-runtime parallelism create a repeatable multi-period limit cycle or hysteresis that is distinguishable from ordinary quota-period throttling, and can runtime-aware pacing damp it?

## Closest established work and exact boundary

| Established result or mechanism | Overlap | Not our contribution | Candidate differentiation |
|---|---|---|---|
| Linux CFS bandwidth control | FOUNDATIONAL | Periodic quota, runtime exhaustion, throttling, replenishment | A distinct multi-period dynamic regime, only if operationally defined and observed |
| SoCC 2025 CPU-limit study | SUBSTANTIAL | CPU limits causing waste, latency, and SLO harm | Temporal regime classification and transition boundary rather than aggregate harm |
| Go 1.25 container-aware GOMAXPROCS | SUBSTANTIAL | Aligning runtime parallelism with cgroup limits; documented tail-latency effects | Joint pacing/parallelism control evaluated against container-aware defaults |
| Generic feedback/autoscaling control | SUBSTANTIAL | Feedback loops, oscillation, hysteresis, pacing | A cgroup/runtime-specific controller only if it exceeds established controllers on justified criteria |
| CBS / Linux SCHED_DEADLINE | FOUNDATIONAL | Periodic runtime budgets, depletion, replenishment, and temporal reservation | A CFS/cgroup-specific multi-period state that the periodic-server null model does not predict |
| Feedback-control real-time scheduling | SUBSTANTIAL | Dynamic-model CPU/QoS control with transient and steady-state objectives | A cgroup-specific nonlinear mechanism and controller compared against an FCS-style null |
| HotSpot container ergonomics and friendlypool | DIRECT / SUBSTANTIAL | Static effective-CPU sizing and dynamic active-worker adaptation under quotas | Persistent behavior after explicit processor, GC, worker, and carrier controls |
| Currentcy, Autothrottle, FIRM, and Sinan | ADJACENT / DIRECT / SUBSTANTIAL | Epoch pacing, throttle-ratio feedback, fine-grained CPU/SLO control, and dependency-aware allocation | Multi-period causal state and damping beyond established pacing/resource-management mechanisms |

## Operational distinction required

Ordinary CFS behavior is period-aligned: a group exhausts quota, is throttled, and receives runtime at replenishment. A claimed limit cycle must exhibit a repeatable state trajectory across multiple quota periods, such as backlog growth and recovery, phase-dependent latency, or a hysteretic transition under increasing versus decreasing load. It must not be defined solely by `nr_throttled`, periodic CPU usage, or one-period stop/resume behavior.

## Candidate contributions

1. A formal operational test distinguishing basic quota waveforms from multi-period limit cycles or hysteresis.
2. A transition map across quota, period, burstiness, runtime parallelism, request backlog, and service-time distribution.
3. A runtime-aware pacing controller evaluated against:
   - default runtime settings;
   - Go 1.25 container-aware GOMAXPROCS or equivalent;
   - fixed concurrency/pacing;
   - no CPU limit where methodologically valid;
   - CBS/SCHED_DEADLINE-inspired periodic-budget prediction;
   - an FCS-style feedback controller;
   - explicit HotSpot `ActiveProcessorCount`/GC/worker controls;
   - friendlypool-style active-worker adaptation;
   - Currentcy-inspired pacing and Autothrottle-style throttle-ratio control where comparable.
4. Cross-runtime evidence only if the same mechanism is demonstrated rather than inferred.

## Claims this paper must not make

- CFS quota-period throttling is a novel oscillation.
- CPU limits harming latency, throughput, utilization, or SLOs is new.
- Container-aware runtime parallelism is new.
- A periodic trace alone proves a feedback limit cycle.
- JVM and Go results are interchangeable without direct measurement.

## Falsifiers

The candidate contribution is weakened or rejected if:

- observed periodicity matches only configured quota periods with no multi-period state;
- backlog or latency has no hysteresis under controlled load sweeps;
- container-aware runtime defaults eliminate the proposed effect;
- results depend on one kernel/runtime version and do not survive controlled replication;
- prior work already defines and controls the same quota/runtime dynamic regime.

## Evidence required to pass this gate

- [x] Foundational CFS, CBS/SCHED_DEADLINE, feedback-control scheduling, runtime-awareness, pacing, and resource-manager evidence is tabulated.
- [x] An operational discriminator requires a declared multi-variable state, multi-period trajectory, up/down sweeps, persistence, and arrival-phase controls.
- [x] Mandatory null models and controller baselines are named and justified.
- [x] Kernel, runtime, controller, arrival, backlog, and workload variables are separated in the evidence requirements.
- [x] Public artifact availability and initial environment/license constraints are inventoried.
- [ ] Cgroup-specific nonlinear-control and backlog-recovery/hysteresis evidence is exhausted.
- [ ] JVM GC/carrier interaction with quota timing and modern cgroup pacing evidence is complete.
- [ ] Forward/backward citation chains from CPU-Limits, Autothrottle, FIRM, and Sinan are complete.
- [ ] Selected baselines pass pinned build, smoke, behavioral, and workload-compatibility checks.
- [x] Candidate claims remain labeled unverified.

## Gate decision

**NOT COMPLETE.** The earlier “closest-work table” and “operational discriminator” blockers are obsolete and have been cleared. The gate remains open for cgroup-specific nonlinear-control and recovery evidence, JVM timing, modern pacing, citation chains, and executable-baseline verification. No experiment-design or implementation gate may start from this reconciliation alone.
