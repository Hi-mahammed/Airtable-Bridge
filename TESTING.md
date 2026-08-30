# Module testing and evidence — v0.2.0

This document records the local regression baseline for
`muhamed/airtable-bridge` v0.2.0. It is an evidence boundary, not a claim of
live API success.

## Baseline

- Manifest: v0.2.0, 37 commands, 17 read, 20 approval-controlled writes.
- Regression target: 221 tests, 0 failures, 0 errors, 0 skips, and 0 warnings
  when run in the restored project environment.
- The release signature must verify against the signed module package; this
  public repository does not distribute signing material.
- The canonical Module credential field is `AIRTABLE_TOKEN`.
- The handler reads it via the `vault_secret` helper. The token is never
  accepted as command input, never logged, and never returned in any response.

## Exact checks

The regression exercises manifest integrity (37 commands, schemas, read/write
governance, capabilities), pure domain logic (resource ID validation, input
validators, CRUD planners, precondition assertions), handler integration
(list, get, create, update, delete across bases, tables, fields, views,
records, webhooks, invites, and collaborators), safety proof chain
determinism, v2 signature readiness, and transport layer behavior. It does not
perform live API writes.

## What the evidence proves

- All 37 manifest commands resolve to handler functions and require receipts.
- Writes remain approval-gated and use the approved payload for idempotency.
- Invalid inputs and missing helpers stop before unsafe provider effects.
- The API token is read from RailCall's vault and is never accepted as input,
  logged, or returned.
- The manifest network boundary is limited to `api.airtable.com`, with no
  subprocess and no filesystem writes declared.
- Every successful result carries a deterministic SHA-256 event chain from
  intent through live observation and decision to verified outcome.

## What is not proved

No live production create, update, delete, or webhook mutation is performed.
Local tests do not prove account-specific permissions, provider uptime, or
marketplace publication. Offline contract tests use fake transports and do not
verify live Airtable API behavior.

## Test breakdown

| File | Tests | Scope |
|---|---|---|
| test_manifest.py | 56 | Manifest structure, command fields, schemas, capabilities, auth |
| test_domain.py | 94 | Pure validators, planners, precondition assertions |
| test_handler.py | 41 | Handler integration with fake transport, all 34 handler paths |
| test_proof.py | 8 | SHA-256 event chain determinism and ordering |
| test_signature.py | 11 | v2 signature readiness, executable structure, capabilities |
| test_transport.py | 11 | Token isolation, HTTP request building, error handling |
| **Total** | **221** | |

## Public evidence locations

- Command-level contract: [COMMANDS.md](COMMANDS.md)
- Public release documentation: [README.md](README.md)
- Test results: [TEST_RESULTS.md](TEST_RESULTS.md)
