---
name: "Nightmare Probe Engine"
author: "SweetKenneth"
github_url: "https://github.com/SweetKenneth/shpbl-nightmare-probe-engine"
description: "Generates falsifiable worst-case fleet hypotheses, compiles each into the smallest bounded targeted-scan probe with a control cohort, and returns a support/falsification verdict that requires discrimination from the controls."
license: "MIT"
tags: ["hypothesis-testing", "vulnerability-management", "scan-planning", "deterministic", "evidence", "provenance", "mcp"]
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
  - name: "nightmare_generate"
    description: "Generate deterministic evidence-seeking security hypotheses from fleet observations"
  - name: "nightmare_compile_probe"
    description: "Compile one hypothesis into a bounded targeted probe plan with optional control cohort"
  - name: "nightmare_evaluate_probe"
    description: "Evaluate target and control observations against a hypothesis and return a falsification/specificity verdict"
resources_exposed: []
prompts_exposed: []
---
## What it does

Generates falsifiable worst-case fleet hypotheses, compiles each into the smallest bounded targeted-scan probe with a control cohort, and returns a support/falsification verdict that requires discrimination from the controls. The operator keeps every actuator: this server has no network client, no filesystem access, no process spawning and no environment reads, and a conformance test asserts that boundary over the source.

- **A theory is not a finding.** Every hypothesis carries the observations that suggested it and the exact probe that could kill it, so nobody argues about a hunch.
- **Probes stay small on purpose.** A hypothesis compiles to the fewest targets that can discriminate it, inside an explicit budget, instead of another fleet-wide sweep.
- **Controls are mandatory for a verdict.** Target support alone cannot mark a hypothesis SUPPORTED; the probe must also separate targets from the tested control cohort.
- **Falsification is a first-class result.** A refuted hypothesis is recorded with its evidence, which is the outcome that actually shrinks the search space.
- **Nothing scans on its own.** The engine plans and judges; the operator's own scanner executes and hands back observations.

## How it works

A zero-dependency stdio MCP server for Node 20+, written in strict TypeScript. Its behaviour is a contract, not a heuristic:

1. `nightmare_generate` derives deterministic, evidence-seeking hypotheses from supplied assets and observations. No observation, no hypothesis.
2. `nightmare_compile_probe` compiles one hypothesis into a bounded probe plan: exact targets, an optional control cohort, and a budget it may not exceed.
3. `nightmare_evaluate_probe` compares target and control observations against the hypothesis and returns SUPPORTED, REFUTED or INCONCLUSIVE with its reasoning.
4. A SUPPORTED verdict requires both target support and discrimination from any tested controls.
5. Malformed, empty or out-of-range input fails closed rather than returning a confident guess.
6. Every run is a pure function of its inputs: same inputs, same hypotheses, same plan, same verdict.

Missing evidence is never a pass — absent, malformed or out-of-range input fails closed rather than returning a confident answer. Identical input produces identical output, so a result reviewed tomorrow is the result produced today. 29 tests cover the behavioural contract, the fail-closed paths, the MCP handshake, unknown-tool and malformed-JSON handling, and a randomised hammer runs 30,000 cases asserting 210,000 invariants.

Known limitations are stated in the repository rather than implied: no Nessus, cloud or network client is embedded. The scan executor boundary must be supplied by an integration; hypotheses are deterministic structures derived from the observations you supply; this is not autonomous discovery of ground truth and carries no probabilistic calibration; a SUPPORTED verdict is evidence of discrimination, not proof of causation; session memory is in process. Exported records are the durable artifact.

## Provenance

This server was written for this submission and copies no third-party implementation code. Its capability lineage is credited explicitly in `PROVENANCE.json`: the practitioner behaviour it addresses was studied in [conard0-git/targeted-nessus-scan](https://github.com/conard0-git/targeted-nessus-scan) by Isaac Conard (conard0-git) (MIT), and the composition draws on capability records from the SHPBL library at [shpbl.com](https://shpbl.com), a governed library of reusable software capabilities and a method for composing them into software neither side previously had. Zero upstream implementation lines and zero SHPBL capability bodies are embedded here; the release is 100% new implementation, MIT licensed, Copyright (c) 2026 Kenneth E. Sweet Jr. Credit does not imply endorsement by Isaac Conard (conard0-git), Tenable or any other party.
