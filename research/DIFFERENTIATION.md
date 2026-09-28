# Differentiation — Container Runtime CPU-Limit Cycles

Review date: 2026-09-28  
Source audit head: `ba40c88bc78221c4e82960877edd32089752dd49`  
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

## Operational distinction required

Ordinary CFS behavior is period-aligned: a group exhausts quota, is throttled, and receives runtime at replenishment. A claimed limit cycle must exhibit a repeatable state trajectory across multiple quota periods, such as backlog growth and recovery, phase-dependent latency, or a hysteretic transition under increasing versus decreasing load. It must not be defined solely by `nr_throttled`, periodic CPU usage, or one-period stop/resume behavior.

## Candidate contributions

1. A formal operational test distinguishing basic quota waveforms from multi-period limit cycles or hysteresis.
2. A transition map across quota, period, burstiness, runtime parallelism, request backlog, and service-time distribution.
3. A runtime-aware pacing controller evaluated against:
   - default runtime settings;
   - Go 1.25 container-aware GOMAXPROCS or equivalent;
   - fixed concurrency/pacing;
   - no CPU limit where methodologically valid.
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

- [ ] Control-theory, oscillatory throttling, backlog recovery, autoscaling, JVM, and Go citation chains are tabulated.
- [ ] A mathematical or algorithmic discriminator between quota waveform and limit cycle is specified.
- [ ] Closest controller baselines are named and justified.
- [ ] Kernel, runtime, and workload variables are separated.
- [ ] Candidate claims remain unverified until closest-work evidence is complete.

## Gate decision

**NOT COMPLETE.** The differentiation target and falsifiers are fixed, but the gate remains open pending the closest-work table, citation chains, and an operational discriminator.
