---
name: "Security Genome Reactor"
author: "SweetKenneth"
github_url: "https://github.com/SweetKenneth/shpbl-security-genome-reactor"
description: "Infers latent security phenotypes from observed findings rather than operator labels, seeds competing scan/policy genomes per phenotype, evolves them under measured fitness, and keeps an explicit parent-before-child ancestry graph for every configuration in production."
license: "MIT"
tags: ["vulnerability-management", "scan-planning", "configuration-management", "deterministic", "provenance", "evidence", "mcp"]
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
  - name: "genome_infer_phenotypes"
    description: "Infer latent security phenotypes from observed findings and compliance behavior, ignoring cloud labels as ground truth"
  - name: "genome_seed"
    description: "Seed multiple candidate security genomes for one phenotype"
  - name: "genome_evolve"
    description: "Run one deterministic evolutionary generation using rehearsal by default or caller-supplied measured fitness keyed by genome id"
  - name: "genome_trial_plan"
    description: "Translate a genome into a bounded target/policy/profile trial plan"
  - name: "genome_lineage"
    description: "Return ancestry in parent-before-child order for a genome from a supplied population/history"
resources_exposed: []
prompts_exposed: []
---
## What it does

Infers latent security phenotypes from observed findings rather than operator labels, seeds competing scan/policy genomes per phenotype, evolves them under measured fitness, and keeps an explicit parent-before-child ancestry graph for every configuration in production. The operator keeps every actuator: this server has no network client, no filesystem access, no process spawning and no environment reads, and a conformance test asserts that boundary over the source.

- **Tags lie; behaviour does not.** Phenotypes are inferred from observed findings and compliance behaviour, so a mislabelled asset group stops driving the scan policy.
- **Configurations compete instead of accumulating.** Each phenotype gets several candidate genomes that are scored, not one inherited profile nobody dares change.
- **Fitness can be measured, not asserted.** Callers may supply real measured fitness per genome; rehearsal is the fallback, never a substitute presented as measurement.
- **Every configuration has parents.** Ancestry is returned in parent-before-child order, so 'why is this profile like this' has an answer.
- **A genome becomes a bounded trial.** The reactor emits an explicit target/policy/profile trial plan rather than mutating anything.

## How it works

A zero-dependency stdio MCP server for Node 20+, written in strict TypeScript. Its behaviour is a contract, not a heuristic:

1. `genome_infer_phenotypes` infers phenotypes from records above an explicit threshold and ignores cloud labels as ground truth.
2. `genome_seed` seeds multiple competing genomes for one phenotype.
3. `genome_evolve` runs one deterministic generation using caller-supplied measured fitness keyed by genome id, or rehearsal when none is supplied.
4. `genome_trial_plan` translates one genome into a bounded target/policy/profile trial plan.
5. `genome_lineage` returns ancestry in parent-before-child order and rejects cycles.
6. Populations below two genomes, unknown ids and invalid thresholds fail closed.

Missing evidence is never a pass — absent, malformed or out-of-range input fails closed rather than returning a confident answer. Identical input produces identical output, so a result reviewed tomorrow is the result produced today. 39 tests cover the behavioural contract, the fail-closed paths, the MCP handshake, unknown-tool and malformed-JSON handling, and a randomised hammer runs 30,000 cases asserting 420,000 invariants.

Known limitations are stated in the repository rather than implied: no scanner or cloud client is embedded; records, fitness and execution are supplied by the caller; rehearsal fitness is an explicit fallback and is labelled as such — it is not a measurement of your fleet; phenotype inference is a deterministic clustering of supplied observations, not a claim about intent or ownership; the reactor proposes trials. It never changes a scan, policy or profile.

## Provenance

This server was written for this submission and copies no third-party implementation code. Its capability lineage is credited explicitly in `PROVENANCE.json`: the practitioner behaviour it addresses was studied in [conard0-git/targeted-nessus-scan](https://github.com/conard0-git/targeted-nessus-scan) by Isaac Conard (conard0-git) (MIT), and the composition draws on capability records from the SHPBL library at [shpbl.com](https://shpbl.com), a governed library of reusable software capabilities and a method for composing them into software neither side previously had. Zero upstream implementation lines and zero SHPBL capability bodies are embedded here; the release is 100% new implementation, MIT licensed, Copyright (c) 2026 Kenneth E. Sweet Jr. Credit does not imply endorsement by Isaac Conard (conard0-git), Tenable or any other party.
