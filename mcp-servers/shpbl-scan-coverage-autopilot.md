---
name: "Scan Coverage Autopilot"
author: "SweetKenneth"
github_url: "https://github.com/SweetKenneth/shpbl-scan-coverage-autopilot"
description: "Finds stale and never-scanned coverage against risk-tier maximum ages, predicts each target's duration from real scan history, and emits a capacity-bounded, risk-weighted next schedule that explicitly reports every overdue target it could not fit and the reason."
license: "MIT"
tags: ["scan-planning", "vulnerability-management", "coverage", "capacity-planning", "deterministic", "provenance", "mcp"]
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
  - name: "scan_analyze_coverage"
    description: "Find risk-weighted scan blind spots using explicit time"
  - name: "scan_plan"
    description: "Generate a deterministic capacity-safe risk-weighted scan plan"
  - name: "scan_compare"
    description: "Compare two sealed scan plans after verifying both seals"
resources_exposed: []
prompts_exposed: []
---
## What it does

Finds stale and never-scanned coverage against risk-tier maximum ages, predicts each target's duration from real scan history, and emits a capacity-bounded, risk-weighted next schedule that explicitly reports every overdue target it could not fit and the reason. The operator keeps every actuator: this server has no network client, no filesystem access, no process spawning and no environment reads, and a conformance test asserts that boundary over the source.

- **Coverage gaps are named, not averaged.** Stale targets are measured against risk-tier maximum ages, and never-scanned targets are called out separately.
- **Durations come from your history.** Prediction uses the median of the exact scanner/target history first, then target-wide history, then your explicit estimate — in that order.
- **The plan fits the window.** Assignments respect scanner capability, scanner capacity and the planning horizon; nothing is scheduled twice.
- **Unschedulable work is reported, not hidden.** Every overdue target that did not fit comes back with a reason, which is the number a coverage conversation actually needs.
- **Plans are comparable.** Each plan is sealed with SHA-256 and can be diffed against another for coverage and load deltas.

## How it works

A zero-dependency stdio MCP server for Node 20+, written in strict TypeScript. Its behaviour is a contract, not a heuristic:

1. `now` must be supplied explicitly; wall-clock time is never silently inferred.
2. Determine staleness from risk-tier maximum ages and identify never-scanned targets.
3. Predict duration from the median exact scanner/target history, then target-wide history, then an explicit estimate.
4. Rank overdue work by deterministic risk and staleness priority.
5. Assign only capability-compatible work, never exceeding scanner capacity or the planning horizon.
6. Schedule each target at most once and report every unscheduled overdue target with a reason.
7. Seal the proposed plan with SHA-256 and support coverage/load comparison between plans.

Missing evidence is never a pass — absent, malformed or out-of-range input fails closed rather than returning a confident answer. Identical input produces identical output, so a result reviewed tomorrow is the result produced today. 25 tests cover the behavioural contract, the fail-closed paths, the MCP handshake, unknown-tool and malformed-JSON handling, and a randomised hammer runs 30,000 cases asserting 120,000 invariants.

Known limitations are stated in the repository rather than implied: no scanner API client is embedded; the plan is a proposal for your scheduler or operator to execute; duration prediction is only as good as the supplied scan history; sparse history falls back to your estimate; risk tiers and maximum ages are caller policy, not a recommendation from this package; the autopilot does not launch, stop or modify scans.

## Provenance

This server was written for this submission and copies no third-party implementation code. Its capability lineage is credited explicitly in `PROVENANCE.json`: the practitioner behaviour it addresses was studied in [packetchaos/navi](https://github.com/packetchaos/navi) by Casey Reid (packetchaos) (MIT), and the composition draws on capability records from the SHPBL library at [shpbl.com](https://shpbl.com), a governed library of reusable software capabilities and a method for composing them into software neither side previously had. Zero upstream implementation lines and zero SHPBL capability bodies are embedded here; the release is 100% new implementation, MIT licensed, Copyright (c) 2026 Kenneth E. Sweet Jr. Credit does not imply endorsement by Casey Reid (packetchaos), Tenable or any other party.
