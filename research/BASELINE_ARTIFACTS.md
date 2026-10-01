# Mandatory baseline artifact inventory

Review date: 2026-09-29

## Status

**SOURCE COMPATIBILITY RECONCILED; EXECUTION AND BEHAVIORAL CONFORMANCE NOT VERIFIED.**

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
| Uber automaxprocs | Official public repository `uber-go/automaxprocs`, pinned signed v1.6.0 release commit `1ea14c35ce47a73089b824e504d1c92eeb61a5a6`; MIT license; `go.mod` declares Go 1.20 | Licensed official implementation; source-compatible candidate, fresh execution pending | Selected zero-cost Go comparator. Run its tests and a cgroup-v2 quota-detection smoke test in the eventual controlled harness; test separately from Go 1.25 defaults because mechanisms and versions differ. |
| HotSpot container support and explicit `ActiveProcessorCount` | Official JDK behavior | Runtime-native baseline | Pin JDK build and GC, record effective processor count, GC threads, ForkJoinPool settings, carrier/worker counts, and container metrics. |
| Friendlypool-style active-worker adaptation | Paper/preprint identified; no public author repository verified in the bounded GitHub search | Paper/specification only | Implement only as a labeled controller reimplementation. Validate active-worker decisions and the stated 10 ms control interval before comparative experiments. |
| Currentcy-style epoch self-pacing | Paper identified; no maintained artifact verified | Historical paper/specification only | Use as an analytical or paper-inspired pacing baseline when comparable. Document differences between energy currentcy and cgroup CPU runtime. |
| Autothrottle | Official Microsoft repository `microsoft/autothrottle`, pinned head `d237d7f3d765ee9ab67bcf4aeb626164a87c6bfa`; archived; inspected controller source reads cgroup-v1 `cpuacct.usage`, `cpu.stat`, and CFS limit files | Official artifact is not selected for direct execution | It assumes a multi-VM/Kubernetes environment, cgroup-v1 interfaces, and no verified top-level license. Under the $0 constraint it is an algorithmic specification only. Use a clean-room, labeled throttle-ratio comparator with unit/trace conformance tests; do not represent it as artifact reproduction. |
| FIRM | Paper identified; no verified author artifact found in the bounded GitHub search | Paper/specification only | Treat as a resource-management comparator or reimplement only the necessary control logic with conformance tests. Do not claim an exact reproduction. |
| Sinan | Public repository `zyqCSL/sinan-gcp`, pinned head `ebe98bb410a1282791010199011f628489b4fd30` | Public research artifact; incompatible with the zero-cost/common-workload gate as-is | README requires Google Cloud deployment and at least a 500-CPU project quota; no top-level license was verified. Do not run or provision it. Use Sinan only as a prior-art boundary unless a separately specified local comparator can be justified without claiming reproduction. |
| Static quota/parallelism matrix | Harness-native | Directly implementable | Include no-limit/request-only, quota/period/burst sweeps, default runtime settings, and explicit parallelism controls. |
| No-limit/request-only control | Harness-native | Directly implementable | Verify that host contention and runtime limits are controlled so this remains a valid null configuration. |

## Selected zero-cost comparator set

The experiment gate should select only comparators that can share one declared arrival process, service demand, cgroup-v2 configuration, and physical telemetry without paid infrastructure:

1. harness-native CFS quota/no-limit/static-parallelism controls;
2. CBS/SCHED_DEADLINE-inspired analytical prediction;
3. Go 1.25 defaults and pinned automaxprocs for the Go arm;
4. HotSpot default container support plus explicit `ActiveProcessorCount`, GC-thread, and worker/carrier controls for the JVM arm;
5. labeled FCS-style, friendlypool-style, Currentcy-inspired, and Autothrottle-style clean-room comparators, each with control-law and trace-conformance tests.

FIRM, Sinan, and the full Autothrottle artifact remain prior-art boundaries, not exact executable baselines, because topology, licensing, cgroup interface, or paid-cloud requirements prevent a truthful common-workload reproduction. This narrowing is methodological, not convenience-based.

## Readiness classes

- **Class A — official, licensed, build gate pending:** Uber automaxprocs.
- **Class B — runtime/kernel-native controls:** Linux CFS, CBS-inspired null, Go 1.25 defaults, HotSpot settings, static/no-limit matrices.
- **Class C — public research artifacts with unresolved license or large cloud dependencies:** Autothrottle; Sinan.
- **Class D — paper/specification only:** FCS-style controller; friendlypool; Currentcy; FIRM.

No class means “reproduced.” Class A and B only identify candidates for the next build or harness-validation gate.

## Workload compatibility

A baseline is comparable only if it can consume the same declared arrival process and service demand, run under the same kernel/cgroup configuration, expose controller decisions, and emit the same physical telemetry. Full microservice resource managers such as Autothrottle, FIRM, and Sinan must not be compared as black boxes against a single-service controller without documenting topology and objective differences.

## Completion gate

The artifact/source compatibility decision is complete for the current mandatory list. Baseline execution remains open until every selected comparator has either:

- a pinned, licensed, build- and smoke-verified implementation plus behavioral validation; or
- a preregistered reimplementation with control-law tests and explicit deviations.

Cloud quota, archived status, absent licensing, or missing code are constraints to report, not reasons to silently remove or weaken a baseline.


## Current acceptance result

- Pinned source and license/environment classification: **PASS**.
- Common-workload compatibility decision: **PASS at specification level**; full FIRM/Sinan/Autothrottle artifacts are excluded from exact execution for documented scientific, licensing, cgroup-version, or zero-cost reasons.
- Fresh build and unit-test evidence: **FAIL / not yet executed**.
- Cgroup-v2 smoke and behavioral conformance: **FAIL / not yet executed**.
- Shared workload-interface validation: **FAIL / not yet executed**.

No discovery metadata, upstream CI file, or source inspection is counted as a local successful build.