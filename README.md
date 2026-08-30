# muhamed/airtable-bridge v0.2.0

Governed Airtable operations for RailCall Station. The module helps an
operator inspect base, table, field, view, record, webhook, and collaborator
state, and prepare or execute approval-controlled mutations. It is for teams
that need a reviewable Airtable workflow with provider truth, receipts, and
deterministic retries — not an unattended workspace-mutating bot.

## Capability map

The manifest contains exactly 37 commands: 17 `read` commands and 20
`write_requires_approval` commands. Read commands cover bases, tables,
fields, views, records, webhooks, invites, and collaborators. The 20 writes
create or update tables, fields, views, records, webhooks, and invites, or
delete tables, fields, views, records, webhooks, and invites. The complete
buyer-facing contract is in [COMMANDS.md](COMMANDS.md).

Every command requires a signed receipt. Read commands do not declare
external effects. Write commands stop at the Station Approval Airlock and
require the exact approved payload before any Airtable mutation. The handler
uses Airtable's provider response as authority and fails closed when
validation, identity, provider state, or post-effect certainty is insufficient.

## Install and Configure

Install `muhamed/airtable-bridge` v0.2.0 through the normal Station module
loader. Configure an Airtable credential with the canonical field:

```text
AIRTABLE_TOKEN=pat...
```

The handler reads the token via the `vault_secret` helper. It is never
accepted as command input, never logged, and never returned in any response.

Use Airtable credentials scoped to the intended bases for evaluation. Never
paste a token into a workflow, receipt, log, screenshot, or documentation.

## Safety and operating model

- **Approval:** all 20 provider writes are Airlock-gated; approval is a
  separate human decision and does not happen inside the handler.
- **Idempotency:** creates and upserts use duplicate-detection guards with
  configurable `on_conflict` (`error`, `ignore`, `update`). Missing helpers
  fail before the provider call.
- **Provider truth:** returned status, record IDs, field values, and
  uncertainty are preserved from Airtable. Local estimates are not presented
  as authority.
- **Receipts and governance:** Station owns receipt persistence, signature,
  approval state, and egress policy. Each command requires a receipt.
- **Network boundary:** the manifest permits `api.airtable.com`; it declares
  no subprocess use and no filesystem writes.
- **Errors:** malformed inputs, missing credentials, provider refusal,
  ambiguous outcomes, and incomplete reads fail closed with the token
  excluded from the error text.

## Quick start

1. Install the module at v0.2.0.
2. Configure an `AIRTABLE_TOKEN` through Station Configure.
3. Use a read command to inspect the intended base, table, or records.
4. Review the receipt and Airlock payload before approving any write.
5. Run against the target Airtable workspace and retain the resulting evidence.

For exact fields, outputs, defaults, caveats, and failure boundaries, see
[COMMANDS.md](COMMANDS.md). For the evidence protocol and known proof limits,
see [TESTING.md](TESTING.md).

## Known limitations

This module does not make live API success claims from local tests. Offline
contract tests use fake transports and do not verify live Airtable API
behavior. Bounded reads report unknown or incomplete state instead of
guessing. Provider rate limits or permission errors are surfaced, not
fabricated as success.

## Evidence index

- Current test report and exact verification scope: [TESTING.md](TESTING.md).
- Exhaustive command reference: [COMMANDS.md](COMMANDS.md).
- Test results: [TEST_RESULTS.md](TEST_RESULTS.md).
