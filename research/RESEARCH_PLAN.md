# Research Plan

## Hypothesis
Selected quota, burstiness, and runtime-parallelism regimes may exhibit repeatable quota-exhaustion/backlog/recovery cycles distinguishable from steady CPU saturation.

## Independent variables
- CPU quota and period
- workload burstiness
- request intensity
- runtime parallelism
- JVM processor-count/runtime settings
- Go GOMAXPROCS
- GC/runtime settings

## Dependent variables
- cgroup throttled periods/time
- CPU utilization
- request backlog
- throughput
- p50/p95/p99 latency
- runtime scheduling metrics
- GC activity
- oscillation period/amplitude where measurable

## Baselines
1. static CPU quota
2. no CPU limit / controlled CPU availability
3. runtime parallelism aligned to quota
4. proposed runtime-aware pacing
