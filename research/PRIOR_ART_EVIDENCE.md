# Prior-Art Evidence — Milestone 1

Status: evidence-backed first pass. Candidate novelty remains **UNVERIFIED** until deeper citation chaining and experimental validation.

## Established facts that constrain novelty

### CPU limits and throttling are established mechanisms
Kubernetes/Linux container CPU limits are enforced through cgroup CPU bandwidth controls. Therefore this project must not claim that CPU limits can throttle applications, create latency, or reduce performance as a new observation.

### CPU-limit performance harm is already published
Shetty et al., **"CPU-Limits kill Performance: Time to rethink Resource Control"**, appeared at ACM SoCC 2025 (DOI: 10.1145/3772052.3772219). The paper empirically argues that CPU limits can cause resource waste and SLO violations for latency-sensitive cloud applications. This is substantial overlap with any broad claim that CPU limits hurt microservice performance.

Verified sources:
- ACM SoCC 2025 program: https://acmsocc.org/2025/schedule.html
- Paper DOI: https://doi.org/10.1145/3772052.3772219
- Public paper copy: https://sarthak-chakraborty.github.io/publications/YAAS_SoCC25.pdf

### Managed runtimes already adapt to container CPU limits
Go 1.25 changed the default GOMAXPROCS behavior on Linux to consider cgroup CPU bandwidth limits and periodically update the value. The Go team explicitly discusses throttling and tail-latency impact when runtime parallelism is mismatched with container CPU limits.

Verified sources:
- Go 1.25 release notes: https://go.dev/doc/go1.25
- Go container-aware GOMAXPROCS article: https://go.dev/blog/container-aware-gomaxprocs

## Overlap classification

| Work | Overlap | Why |
|---|---|---|
| Linux/Kubernetes cgroup CPU-limit semantics | FOUNDATIONAL | Defines the quota/throttling mechanism under study. |
| Shetty et al., SoCC 2025 | SUBSTANTIAL | Directly studies harmful performance/SLO effects of CPU limits in cloud-native workloads. |
| Go 1.25 container-aware GOMAXPROCS | SUBSTANTIAL | Directly addresses runtime-parallelism mismatch with cgroup CPU limits and throttling. |

## Claims we must NOT make

- CPU throttling under cgroup CPU limits is new.
- CPU limits degrading latency or SLOs is new.
- Managed runtimes being sensitive to CPU limits is new.
- Aligning runtime parallelism with container CPU limits is itself new.

## Candidate defensible gap — UNVERIFIED

The remaining candidate is narrower: determine whether specific combinations of quota period/budget, burstiness, backlog, and managed-runtime parallelism create a repeatable **dynamic limit-cycle or hysteretic regime**, characterize its transition boundary and temporal signature, distinguish it from ordinary steady throttling, and test whether a runtime-aware pacing controller damps that oscillation.

This is a research hypothesis, not a novelty claim. A deeper search must explicitly target control-theoretic analyses, oscillatory CPU throttling, quota-period dynamics, feedback loops, autoscaling interactions, JVM scheduling/GC interactions, Go scheduler interactions, and prior pacing/controllers.

## Next search layer

1. "CFS bandwidth" oscillation / periodic throttling / limit cycle
2. CPU quota hysteresis backlog burst recovery
3. cgroup quota feedback control latency
4. JVM container CPU quota GC scheduler throttling
5. Go GOMAXPROCS quota burst throttling
6. citation chains from SoCC 2025 and container-aware GOMAXPROCS
