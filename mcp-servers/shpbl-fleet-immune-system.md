---
name: "Fleet Immune System"
author: "SweetKenneth"
github_url: "https://github.com/SweetKenneth/shpbl-fleet-immune-system"
description: "Treats normalized scan findings as immune signals: discovers co-occurring fleet threat patterns, synthesizes non-executing defensive recipes, retains successful outcomes as recallable memory, and targets adjacent cohorts likely to show the same variant."
license: "MIT"
tags: ["vulnerability-management", "detection-engineering", "threat-patterns", "deterministic", "evidence", "provenance", "mcp"]
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
  - name: "immune_discover"
    description: "Discover co-occurring emergent fleet threat patterns from normalized defensive scan signals"
  - name: "immune_synthesize"
    description: "Synthesize a non-executing defensive antibody recipe from a threat pattern"
  - name: "immune_cycle"
    description: "Plan one fleet immune cycle with variants, memory recall and adjacent cohort targeting"
  - name: "immune_crystallize"
    description: "Persist a successful pattern/antibody outcome into the server-local immune memory for later recall"
  - name: "immune_recall"
    description: "Recall the closest retained immune-memory crystal for a threat pattern"
  - name: "immune_breed"
    description: "Breed two non-executing antibody recipes into a deterministic next-generation candidate without escalating destructive action"
resources_exposed: []
prompts_exposed: []
---
## What it does

Treats normalized scan findings as immune signals: discovers co-occurring fleet threat patterns, synthesizes non-executing defensive recipes, retains successful outcomes as recallable memory, and targets adjacent cohorts likely to show the same variant. The operator keeps every actuator: this server has no network client, no filesystem access, no process spawning and no environment reads, and a conformance test asserts that boundary over the source.

- **Findings stop being a flat list.** Co-occurrence across the fleet is surfaced as a supported pattern with the signals that support it, so related exposure is treated as one condition.
- **Defensive recipes never execute.** A synthesized antibody is a described, reviewable response plan — the server has no actuator and cannot run it.
- **Successful responses are remembered.** A crystallized outcome is recallable by pattern similarity, so the second occurrence does not restart the analysis.
- **Adjacent cohorts get named.** The cycle planner points at neighbours in the supplied topology that are likely to carry the same variant, before they are scanned.
- **Breeding cannot escalate.** Combining two recipes is deterministic and may not introduce destructive action absent from both parents.

## How it works

A zero-dependency stdio MCP server for Node 20+, written in strict TypeScript. Its behaviour is a contract, not a heuristic:

1. `immune_discover` returns co-occurring patterns that meet an explicit minimum support; unsupported coincidence is not a pattern.
2. `immune_synthesize` produces a non-executing antibody recipe from a pattern and its signals.
3. `immune_cycle` plans one cycle: patterns, variants, memory recall and adjacent-cohort targets from the supplied topology.
4. `immune_crystallize` retains a pattern/antibody/outcome above a confidence floor into session-local immune memory.
5. `immune_recall` returns the closest retained crystal for a pattern, or nothing when similarity is below the threshold.
6. `immune_breed` deterministically combines two recipes and refuses to escalate destructive action beyond both parents.
7. Unknown fields, malformed signals and out-of-range thresholds fail closed.

Missing evidence is never a pass — absent, malformed or out-of-range input fails closed rather than returning a confident answer. Identical input produces identical output, so a result reviewed tomorrow is the result produced today. 38 tests cover the behavioural contract, the fail-closed paths, the MCP handshake, unknown-tool and malformed-JSON handling, and a randomised hammer runs 30,000 cases asserting 210,000 invariants.

Known limitations are stated in the repository rather than implied: no scanner, cloud or network client is embedded; normalized signals are supplied by the caller; pattern support is a co-occurrence measure over the signals you supply, not a causal claim; antibody recipes are descriptions for human or adapter execution. Nothing here applies a change; immune memory is session-local; exported crystals are the durable artifact.

## Provenance

This server was written for this submission and copies no third-party implementation code. Its capability lineage is credited explicitly in `PROVENANCE.json`: the practitioner behaviour it addresses was studied in [conard0-git/targeted-nessus-scan](https://github.com/conard0-git/targeted-nessus-scan) by Isaac Conard (conard0-git) (MIT), and the composition draws on capability records from the SHPBL library at [shpbl.com](https://shpbl.com), a governed library of reusable software capabilities and a method for composing them into software neither side previously had. Zero upstream implementation lines and zero SHPBL capability bodies are embedded here; the release is 100% new implementation, MIT licensed, Copyright (c) 2026 Kenneth E. Sweet Jr. Credit does not imply endorsement by Isaac Conard (conard0-git), Tenable or any other party.
