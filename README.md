# 06 · Secure Code Sandbox

A planned isolated execution service with explicit trust boundaries, denied egress, resource limits, and execution deadlines.

**Status: planning — implementation has not started.** This README describes intended scope and acceptance criteria. It does not establish working behavior, completed tests, security guarantees, or measured performance.

Week 6 of an eight-repository agent-systems portfolio.

## Planned scope

- Select and document a microVM or hardened-container backend against an explicit threat model.
- Enforce filesystem, network-egress, process, CPU, memory, output, and lifetime policies.
- Report execution outcomes and verify cleanup after timeout, cancellation, and worker failure.

## Planned setup and observability

The implementation is intended to provide a `docker-compose.yml` and `Makefile` so a reviewer can use `make demo` for a deterministic offline demonstration and `make test` for offline regression checks. **These commands and their supporting files are not implemented yet.** Live-model runs will be optional and separately documented with configuration and cost assumptions.

Every model call and tool execution is intended to emit an OpenTelemetry span. Planned aggregate reporting includes token usage, estimated USD per 1,000 requests, p50/p95 latency, and p50/p95 time to first token (TTFT) where streaming applies. Offline fixtures can verify instrumentation and behavior; live-provider performance requires separate measurements.

Implementation choices will remain inspectable, with explicit contracts and documented dependencies.

See [PRODUCTION_CHECKLIST.md](PRODUCTION_CHECKLIST.md) for shared acceptance criteria.

## Proposed architecture and tradeoffs

An authenticated execution broker validates requests and launches disposable workers through a narrowly scoped backend adapter. The backend applies isolation and resource policies before executing code. A collector bounds output and returns structured results while a supervisor enforces TTL and cleanup.

MicroVMs and containers have different isolation, startup, and operational costs. A shared host kernel is a material boundary for a container design. A Compose-based local setup alone does not demonstrate secure isolation; claims must match the selected backend and adversarial verification.

## Roadmap and acceptance criteria

Complete the shared reproducibility and observability checklist, then implement and verify these project-specific criteria:

- [ ] Write the threat model, trusted components, supported host requirements, and residual risks before selecting the backend.
- [ ] Demonstrate denied egress, restricted filesystem access, resource enforcement, and TTL termination with recorded adversarial cases.
- [ ] Demonstrate cleanup after worker crashes and cancellation without exposing host credentials or a Docker socket to submitted code.
- [ ] Compare startup, execution, and cleanup latency under the same policy; retain all isolation requirements in any optimized configuration.

## Candidate failure modes

These are design risks to investigate, **not tested or observed findings**:

- An isolation escape or unintended mount exposes host data or control interfaces.
- DNS, metadata endpoints, or alternate network paths bypass the intended egress policy.
- Fork bombs, excessive output, or incomplete cleanup exhaust shared resources.

The implementation will add reproduction cases, actual outcomes, and mitigations as evidence becomes available.

## Baseline versus optimized metrics

No benchmark has been run. The following comparison is planned:

- **Baseline:** The selected backend with the full declared isolation policy and fresh workers.
- **Optimized:** Startup or scheduling improvements that retain the identical isolation and resource policy.

| Metric | Baseline | Optimized |
| --- | --- | --- |
| Input/output/total tokens per request | Not yet measured | Not yet measured |
| Estimated USD per 1,000 requests | Not yet measured | Not yet measured |
| End-to-end latency p50 | Not yet measured | Not yet measured |
| End-to-end latency p95 | Not yet measured | Not yet measured |
| Streaming TTFT p50, where applicable | Not yet measured | Not yet measured |
| Streaming TTFT p95, where applicable | Not yet measured | Not yet measured |
| Project-specific quality and failure rate | Not yet measured | Not yet measured |

Future results must identify the workload revision, model/provider configuration, environment, concurrency, sample counts, and quality criteria. Cost estimates must disclose pricing date and assumptions. Report the complete workload, including errors and retries, and retain raw results so baseline and optimized runs can be reproduced. Do not substitute target values for measurements.

## Portfolio

- [01 · Raw Agent Tool Loop](https://github.com/sn41lz/agent-systems-01-tool-loop)
- [02 · Context Window Engine](https://github.com/sn41lz/agent-systems-02-context-window)
- [03 · Hierarchical Agent Memory](https://github.com/sn41lz/agent-systems-03-hierarchical-memory)
- [04 · Multi-Agent Orchestrator](https://github.com/sn41lz/agent-systems-04-multi-agent-orchestrator)
- [05 · Agent Evaluation Harness](https://github.com/sn41lz/agent-systems-05-eval-harness)
- [06 · Secure Code Sandbox](https://github.com/sn41lz/agent-systems-06-secure-sandbox)
- [07 · Durable Agent Runtime](https://github.com/sn41lz/agent-systems-07-durable-runtime)
- [08 · Agent Platform API and SDKs](https://github.com/sn41lz/agent-systems-08-api-sdk)
