---
name: "Counterfactual Exposure Planner"
author: "SweetKenneth"
github_url: "https://github.com/SweetKenneth/shpbl-counterfactual-exposure-planner"
description: "A deterministic what-if engine for exposure data: clones a sealed exposure graph, applies bounded interventions, recomputes weighted risk and reachable attack surface, then ranks the resulting futures and marks the Pareto-efficient ones before production changes."
license: "MIT"
tags: ["exposure-management", "risk-scoring", "attack-surface", "prioritization", "deterministic", "provenance", "mcp"]
tier: "contributed"
integrations: []
date_added: 2026-09-14
contribution_agreement_date: 2026-09-14T20:20:04Z
works_with_tenable_hexa_mcp: false
transport: "stdio"
runtime: "node"
auth_method: "none"
compatible_clients: ["Claude Code", "Claude Desktop", "Cursor"]
tools_exposed:
  - name: "exposure_baseline"
    description: "Measure a sealed exposure graph without mutating it"
  - name: "exposure_simulate"
    description: "Simulate one intervention scenario against current exposure state"
  - name: "exposure_rank"
    description: "Rank multiple counterfactual scenarios deterministically and identify Pareto-efficient options"
resources_exposed: []
prompts_exposed: []
---
## What it does

A deterministic what-if engine for exposure data: clones a sealed exposure graph, applies bounded interventions, recomputes weighted risk and reachable attack surface, then ranks the resulting futures and marks the Pareto-efficient ones before production changes. The operator keeps every actuator: this server has no network client, no filesystem access, no process spawning and no environment reads, and a conformance test asserts that boundary over the source.

- **"Patch this or isolate that?" gets a measured answer.** Both futures are computed against the same sealed baseline instead of argued.
- **Your input graph is never mutated.** Every scenario runs on a clone, so a planning session cannot corrupt the state of record.
- **Reachability, not just counts.** Scenarios are scored on weighted risk, reachable attack surface and critical reachable findings, plus their own cost.
- **Inferior options are labelled.** Pareto marking stops a scenario that is worse on every axis from being presented as a reasonable choice.
- **Rankings are reproducible.** Deterministic utility with stable tie-breaks means the same data ranks the same way in tomorrow's review.

## How it works

A zero-dependency stdio MCP server for Node 20+, written in strict TypeScript. Its behaviour is a contract, not a heuristic:

1. Validate and clone the baseline exposure graph; caller input is never mutated.
2. Support bounded interventions: patch a finding, isolate an asset, reduce exposure probability, or vary criticality as a stated assumption.
3. Recompute weighted risk, reachable attack surface, critical reachable findings and intervention cost for every scenario.
4. Seal both baseline graph and scenario with SHA-256 canonical digests.
5. Rank scenarios deterministically by utility with stable tie-breaks.
6. Mark Pareto-efficient scenarios so a dominated scenario is not presented as equally attractive.
7. Unknown targets and out-of-range values fail closed.

Missing evidence is never a pass — absent, malformed or out-of-range input fails closed rather than returning a confident answer. Identical input produces identical output, so a result reviewed tomorrow is the result produced today. 25 tests cover the behavioural contract, the fail-closed paths, the MCP handshake, unknown-tool and malformed-JSON handling, and a randomised hammer runs 30,000 cases asserting 120,000 invariants.

Known limitations are stated in the repository rather than implied: results describe the graph you supply; missing assets, edges or findings produce a confident answer about an incomplete world; no scanner or vendor API client is embedded and nothing is changed in production; criticality variation is an explicit assumption, not a measurement; cost is the caller's declared intervention cost, not an economic model.

## Provenance

This server was written for this submission and copies no third-party implementation code. Its capability lineage is credited explicitly in `PROVENANCE.json`: the practitioner behaviour it addresses was studied in [packetchaos/navi](https://github.com/packetchaos/navi) by Casey Reid (packetchaos) (MIT), and the composition draws on capability records from the SHPBL library at [shpbl.com](https://shpbl.com), a governed library of reusable software capabilities and a method for composing them into software neither side previously had. Zero upstream implementation lines and zero SHPBL capability bodies are embedded here; the release is 100% new implementation, MIT licensed, Copyright (c) 2026 Kenneth E. Sweet Jr. Credit does not imply endorsement by Casey Reid (packetchaos), Tenable or any other party.
