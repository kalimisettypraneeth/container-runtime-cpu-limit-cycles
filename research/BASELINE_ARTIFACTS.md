# Mandatory baseline artifact inventory

Review date: 2026-09-29

## Status

**PUBLIC ARTIFACT INVENTORY COMPLETE FOR CURRENT MANDATORY BASELINES; EXECUTION NOT VERIFIED.**

This inventory distinguishes discoverable code from a reproducible baseline. Repository metadata, README instructions, current public heads, and top-level license files were checked where available. No baseline is marked runnable or reproduced because no build, deployment, smoke test, or behavioral conformance test was executed in this review.

## Reproducibility rule

A repository is only discovery evidence. Mark a baseline executable only after recording:

1. repository and pinned revision;
2. usable license or explicit permission;
3. successful build commands;
4. kernel, cgroup version, runtime, container, host, and hardware manifest;
5. smoke-test output; and
6. a behavioral check against the mechanism described by the paper.

If official code is absent, incompatible, or too environment-specific, the replacement must be labeled a reimplementation, include conformance tests, and document deviations. It must not be presented as the original artifact.

## Inventory

| Mandatory baseline or null model | Artifact evidence checked | Initial classification | Experiment decision |
|---|---|---|---|
| Linux CFS bandwidth control | Kernel mechanism exposed by the test host; official kernel documentation and source are authoritative | Harness-native kernel baseline | Pin kernel/cgroup version and record `cpu.max`, `cpu.max.burst`, slice setting, `cpu.stat`, topology, and scheduler configuration. Validate the ordinary one-period depletion/replenishment waveform before testing multi-period claims. |
| CBS/SCHED_DEADLINE periodic-budget null | Linux implementation and published equations; no external research harness required | Kernel/analytical null model | Implement prediction and trace checks separately from the candidate controller. Do not equate CFS bandwidth with SCHED_DEADLINE without stating the abstraction boundary. |
| Feedback-control scheduling (FCS-style) | Papers identified; no maintained author artifact verified in the bounded GitHub search | Paper/specification only | Use a preregistered reimplementation with control-law unit tests, synthetic step-response tests, and explicit tuning procedure. |
| Go 1.25 container-aware `GOMAXPROCS` | Official Go toolchain behavior | Runtime-native baseline | Pin exact Go build and record `GOMAXPROCS`, quota, period, effective processor count, and runtime metrics. |
| Uber automaxprocs | Official public repository `uber-go/automaxprocs`, pinned discovery head `1ea14c35ce47a73089b824e504d1c92eeb61a5a6`; README and MIT license verified | Licensed official implementation; execution pending | Strong build/smoke candidate for Go workloads. Test separately from Go 1.25 defaults because the mechanisms and versions differ. |
| HotSpot container support and explicit `ActiveProcessorCount` | Official JDK behavior | Runtime-native baseline | Pin JDK build and GC, record effective processor count, GC threads, ForkJoinPool settings, carrier/worker counts, and container metrics. |
| Friendlypool-style active-worker adaptation | Paper/preprint identified; no public author repository verified in the bounded GitHub search | Paper/specification only | Implement only as a labeled controller reimplementation. Validate active-worker decisions and the stated 10 ms control interval before comparative experiments. |
| Currentcy-style epoch self-pacing | Paper identified; no maintained artifact verified | Historical paper/specification only | Use as an analytical or paper-inspired pacing baseline when comparable. Document differences between energy currentcy and cgroup CPU runtime. |
| Autothrottle | Official Microsoft repository `microsoft/autothrottle`, pinned discovery head `d237d7f3d765ee9ab67bcf4aeb626164a87c6bfa`; repository is archived | Official artifact with major environment and licensing constraints | README describes five Azure VMs and a reproduction of Table 1, excluding Sinan, in less than 100 hours. No top-level license was verified. Do not vendor or execute until licensing is cleared; a local algorithmic reimplementation must be labeled and behaviorally validated. |
| FIRM | Paper identified; no verified author artifact found in the bounded GitHub search | Paper/specification only | Treat as a resource-management comparator or reimplement only the necessary control logic with conformance tests. Do not claim an exact reproduction. |
| Sinan | Public repository `zyqCSL/sinan-gcp`, pinned discovery head `ebe98bb410a1282791010199011f628489b4fd30` | Public research artifact with major environment and licensing constraints | README requires Google Cloud tooling, Python 2.7 for plotting, and a Compute Engine project quota of at least 500 CPUs. No top-level license was verified. Full reproduction is not a default baseline; any reduced local version must be labeled and its scope narrowed. |
| Static quota/parallelism matrix | Harness-native | Directly implementable | Include no-limit/request-only, quota/period/burst sweeps, default runtime settings, and explicit parallelism controls. |
| No-limit/request-only control | Harness-native | Directly implementable | Verify that host contention and runtime limits are controlled so this remains a valid null configuration. |

## Readiness classes

- **Class A — official, licensed, build gate pending:** Uber automaxprocs.
- **Class B — runtime/kernel-native controls:** Linux CFS, CBS-inspired null, Go 1.25 defaults, HotSpot settings, static/no-limit matrices.
- **Class C — public research artifacts with unresolved license or large cloud dependencies:** Autothrottle; Sinan.
- **Class D — paper/specification only:** FCS-style controller; friendlypool; Currentcy; FIRM.

No class means “reproduced.” Class A and B only identify candidates for the next build or harness-validation gate.

## Workload compatibility

A baseline is comparable only if it can consume the same declared arrival process and service demand, run under the same kernel/cgroup configuration, expose controller decisions, and emit the same physical telemetry. Full microservice resource managers such as Autothrottle, FIRM, and Sinan must not be compared as black boxes against a single-service controller without documenting topology and objective differences.

## Completion gate

The public-artifact discovery item is complete for the current mandatory list. Baseline compatibility remains open until every selected comparator has either:

- a pinned, licensed, build- and smoke-verified implementation plus behavioral validation; or
- a preregistered reimplementation with control-law tests and explicit deviations.

Cloud quota, archived status, absent licensing, or missing code are constraints to report, not reasons to silently remove or weaken a baseline.
