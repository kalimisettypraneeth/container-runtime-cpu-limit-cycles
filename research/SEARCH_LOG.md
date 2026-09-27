# Prior-Art Search Log

| Date | Database/Search Engine | Query | Filters | Number/Type of Results | Relevant Results |
|---|---|---|---|---|---|
| 2026-09-26 | Kubernetes documentation | CPU limits throttling cgroups | official documentation | documentation | CPU limits are kernel/cgroup-enforced; throttling itself is not a novelty claim. |
| 2026-09-27 | ACM SoCC / web | "CPU-Limits kill Performance" | SoCC 2025 / primary or author-hosted evidence | conference paper | Shetty et al., CPU-Limits kill Performance: Time to rethink Resource Control, SoCC 2025, DOI 10.1145/3772052.3772219. Direct evidence that broad CPU-limit performance/SLO harm is established. |
| 2026-09-27 | Go official documentation | Go 1.25 GOMAXPROCS container CPU limit | go.dev only | release notes + runtime engineering article | Go 1.25 makes GOMAXPROCS container-aware; Go documents cgroup quota, throttling, and tail-latency interaction. |
