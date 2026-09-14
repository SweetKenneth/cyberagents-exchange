---
name: "Remediation Flight Recorder"
author: "SweetKenneth"
github_url: "https://github.com/SweetKenneth/shpbl-remediation-flight-recorder"
description: "Runs security remediation as one auditable transaction: freezes the pre-change exposure snapshot, computes direct and transitive blast radius, gates on policy and explicit approval, binds external execution receipts, verifies the post-change state, and enters rollback when verification fails — all sealed in a SHA-256 evidence chain."
license: "MIT"
tags: ["remediation", "change-control", "blast-radius", "rollback", "evidence", "deterministic", "provenance", "mcp"]
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
  - name: "remediation_preflight"
    description: "Create a policy-gated remediation transaction and compute blast radius and rollback plan"
  - name: "remediation_approve"
    description: "Record explicit human/operator approval for a preflighted transaction"
  - name: "remediation_record_execution"
    description: "Bind execution receipts to an approved transaction; this package does not execute remote commands itself"
  - name: "remediation_verify"
    description: "Compare a post-remediation exposure snapshot with declared expectations"
  - name: "remediation_record_rollback"
    description: "Bind rollback receipts to a transaction that entered rollback state"
  - name: "remediation_verify_ledger"
    description: "Verify both payload hashes and the local tamper-evident evidence chain"
resources_exposed: []
prompts_exposed: []
---
## What it does

Runs security remediation as one auditable transaction: freezes the pre-change exposure snapshot, computes direct and transitive blast radius, gates on policy and explicit approval, binds external execution receipts, verifies the post-change state, and enters rollback when verification fails — all sealed in a SHA-256 evidence chain. The operator keeps every actuator: this server has no network client, no filesystem access, no process spawning and no environment reads, and a conformance test asserts that boundary over the source.

- **The pre-state is frozen before anything moves.** Nobody can reconstruct 'what it looked like before' from memory after a bad change.
- **Blast radius is computed, not estimated.** Direct and transitive impact comes from the supplied dependency graph before approval is even possible.
- **Denied means denied.** A transaction that fails policy cannot be approved or executed; the state machine refuses out-of-order transitions.
- **Execution is external and receipted.** The server never SSHes, calls an API or changes a device; it binds the receipts your executor returns.
- **Verification failure has a defined next move.** Failed post-state verification enters rollback when a rollback plan exists, and the rollback is receipted too.
- **The whole flight is hash-linked.** Every lifecycle event is chained with SHA-256 over canonical data, so tampering is detectable after the fact.

## How it works

A zero-dependency stdio MCP server for Node 20+, written in strict TypeScript. Its behaviour is a contract, not a heuristic:

1. `remediation_preflight` seals the exposure snapshot, computes blast radius, evaluates policy, builds the rollback plan and appends evidence.
2. Denied transactions can never be approved or executed.
3. `remediation_approve` records an explicit named approver; approval is never implied.
4. `remediation_record_execution` binds external executor receipts to an approved transaction.
5. `remediation_verify` compares the declared post-state expectation against the supplied post-change snapshot.
6. Failed verification enters rollback where a plan exists; `remediation_record_rollback` closes the transaction as rolled back or failed.
7. `remediation_verify_ledger` re-verifies payload hashes and the chain independently of session trust.
8. Every out-of-order transition fails closed.

Missing evidence is never a pass — absent, malformed or out-of-range input fails closed rather than returning a confident answer. Identical input produces identical output, so a result reviewed tomorrow is the result produced today. 33 tests cover the behavioural contract, the fail-closed paths, the MCP handshake, unknown-tool and malformed-JSON handling, and a randomised hammer runs 30,000 cases asserting 180,000 invariants.

Known limitations are stated in the repository rather than implied: no remote execution or vendor API client is embedded; an operator or adapter supplies execution and rollback receipts; blast radius follows the supplied dependency graph — an incomplete graph produces an incomplete impact model; policy correctness depends on the caller-supplied policy; the ledger is tamper-evident within the supplied chain; durable external anchoring is out of scope for this local server.

## Provenance

This server was written for this submission and copies no third-party implementation code. Its capability lineage is credited explicitly in `PROVENANCE.json`: the practitioner behaviour it addresses was studied in [packetchaos/navi](https://github.com/packetchaos/navi) by Casey Reid (packetchaos) (MIT), and the composition draws on capability records from the SHPBL library at [shpbl.com](https://shpbl.com), a governed library of reusable software capabilities and a method for composing them into software neither side previously had. Zero upstream implementation lines and zero SHPBL capability bodies are embedded here; the release is 100% new implementation, MIT licensed, Copyright (c) 2026 Kenneth E. Sweet Jr. Credit does not imply endorsement by Casey Reid (packetchaos), Tenable or any other party.
