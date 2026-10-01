# Differentiation — Container Runtime CPU-Limit Cycles

Review date: 2026-09-30  
Reconciled evidence base: `master@cc1410c9e4db9b60d2568f177dec6e007cb052ce`  
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
| Currentcy, Autothrottle, FIRM, Sinan, Cilantro, and Ursa | ADJACENT / DIRECT / SUBSTANTIAL | Epoch pacing, throttle-ratio feedback, online performance allocation, analytical SLA decomposition, fine-grained CPU/SLO control, and dependency-aware allocation | Multi-period causal state and damping beyond established pacing/resource-management mechanisms |

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

## Refined causal discriminator

The candidate state vector is `x(t) = [backlog, achieved service rate, p99 latency, throttled time/fraction, runtime parallelism, GC/compiler/worker-or-carrier activity]`, sampled at a resolution sufficient to align observations with configured quota periods. The preferred mechanism is supported only if, at matched offered load and service demand:

1. up- and down-sweeps produce distinct transition or recovery paths over multiple periods;
2. randomized arrival-phase and fixed-phase controls do not remove the path difference;
3. the path persists after container-aware processor sizing and explicit GC/worker/carrier controls;
4. a CBS-style periodic-budget predictor, ordinary queue/backlog model, and FCS/Autothrottle-style controllers fail in preregistered ways; and
5. the claimed controller improves recovery without merely reducing offered work or silently increasing CPU entitlement.

If these conditions fail, the result must be reported as ordinary quota waveform, queueing/backlog recovery, runtime overcommitment, or established feedback behavior rather than a new limit cycle.

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
- [x] Bounded cgroup-specific nonlinear-control and backlog-recovery/hysteresis search is documented; no direct mechanism match was identified, and the negative result is not treated as novelty proof.
- [x] Official JVM processor-count/GC/compiler/ForkJoin coupling and modern cgroup burst/pacing evidence are reconciled; a quota-phase GC/carrier loop remains an empirical hypothesis, not a literature fact.
- [x] Backward/adjacent citation chains from CPU-Limits, Autothrottle, FIRM, Sinan, Cilantro, and Ursa are documented with bounded forward searching.
- [ ] Selected baselines pass pinned build, smoke, behavioral, and workload-compatibility checks.
- [x] Candidate claims remain labeled unverified.

## Gate decision

**NOT COMPLETE.** The literature/mechanism/citation-chain blockers are now reconciled as bounded evidence, and the operational falsifier is explicit. Novelty is still unverified. The sole remaining gate is executable-baseline evidence: selected zero-cost comparators must pass pinned build, smoke, control-law/behavioral, and common-workload-interface checks with GitHub readback. No experiment-design or implementation gate may start before that evidence exists.
