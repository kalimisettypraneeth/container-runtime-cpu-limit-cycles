# Prior-Art Evidence — Milestone 1

Review date: 2026-09-28.

Status: deeper evidence-backed audit. Candidate novelty remains **UNVERIFIED** until citation chaining and experimental validation are complete.

## Established facts that constrain novelty

### CFS bandwidth already defines periodic exhaustion and replenishment

Linux CFS bandwidth control assigns quota over a configurable period. Once a cgroup exhausts its quota, its tasks are throttled until the next period replenishes runtime. That periodic stop/resume pattern is established kernel behavior and cannot itself be called a novel limit cycle.

Verified source:
- Linux kernel CFS bandwidth control: https://docs.kernel.org/scheduler/sched-bwc.html

### CPU-limit performance harm is already published

Shetty et al., **"CPU-Limits kill Performance: Time to rethink Resource Control"**, appeared at ACM SoCC 2025 (DOI: 10.1145/3772052.3772219). It establishes that CPU limits can cause resource waste and SLO violations in latency-sensitive cloud applications.

Verified sources:
- https://acmsocc.org/2025/schedule.html
- https://doi.org/10.1145/3772052.3772219
- https://sarthak-chakraborty.github.io/publications/YAAS_SoCC25.pdf

### Managed runtimes already adapt to container CPU limits

Go 1.25 changed Linux default GOMAXPROCS behavior to consider cgroup CPU bandwidth limits and update periodically. Go's official explanation covers early quota exhaustion, throttling for the remainder of a quota period, and resulting tail-latency effects.

Verified sources:
- https://go.dev/doc/go1.25
- https://go.dev/blog/container-aware-gomaxprocs

## Overlap classification

| Work | Overlap | Why |
|---|---|---|
| Linux CFS bandwidth documentation | FOUNDATIONAL | Defines quota periods, exhaustion, replenishment, and throttling. |
| Shetty et al., SoCC 2025 | SUBSTANTIAL | Studies CPU-limit performance and SLO harm in cloud workloads. |
| Go 1.25 container-aware GOMAXPROCS | SUBSTANTIAL | Addresses runtime-parallelism mismatch, quota exhaustion, throttling, and latency. |

## Claims this project must not make

- Periodic quota exhaustion and replenishment are a novel oscillation.
- CPU limits degrading latency, throughput, or SLOs is new.
- Managed runtimes being sensitive to cgroup limits is new.
- Aligning runtime parallelism with a container limit is itself new.

## Candidate defensible gap — UNVERIFIED

A defensible contribution would require evidence of a dynamic regime beyond ordinary per-period throttling: a repeatable multi-period limit cycle or hysteresis caused by interactions among burstiness, backlog, quota parameters, runtime scheduling, and feedback. The experiment must define observables that distinguish this regime from the kernel's basic quota waveform, map its transition boundary, and test whether a runtime-aware pacing controller damps it.

This is a hypothesis. The differentiation gate must still search control-theoretic analyses, oscillatory throttling, backlog recovery, autoscaling feedback, JVM scheduling/GC, Go scheduler behavior, and prior pacing controllers.
