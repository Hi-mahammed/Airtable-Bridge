# Command reference — muhamed/airtable-bridge v0.2.0

This index keeps all 37 buyer-facing command IDs explicit. Every command
requires a signed receipt. `read` has no declared external side effect;
`write_requires_approval` means Station Approval Airlock plus the approved
payload. Inputs and outputs below use the manifest field names; unspecified
defaults are not invented.

| # | Exact command | Title | Mode / risk | Provider | Inputs (required; optional) | Output fields | Side effects, use, failure/caveat |
|---:|---|---|---|---|---|---|---|
|1|`airtable.list_bases`|List Airtable bases accessible to the token|read / low|airtable|none; offset, page_size|bases, offset, http_status|none; lookup; invalid/provider error fails closed|
|2|`airtable.get_base`|Retrieve a single base by id|read / low|airtable|base_id|base, http_status|none; provider truth; missing/not-found fails|
|3|`airtable.get_base_schema`|Retrieve the full schema of a base|read / low|airtable|base_id|tables, http_status|none; composite read; incomplete data is not guessed|
|4|`airtable.list_tables`|List tables in an Airtable base|read / low|airtable|base_id|tables, http_status|none; bounded provider list|
|5|`airtable.get_table`|Retrieve a single table by id|read / low|airtable|base_id, table_id|table, http_status|none; provider truth; missing/not-found fails|
|6|`airtable.create_table`|Create a new table in a base|write_requires_approval / high|airtable|base_id, table; |table_id, table, changed, http_status|external write; approval/idempotency; provider refusal visible|
|7|`airtable.update_table`|Update a table name or description|write_requires_approval / high|airtable|base_id, table_id, table; expected_name|table_id, before, after, changed, http_status|external write; approval/idempotency; precondition checked|
|8|`airtable.delete_table`|Delete a table from a base|write_requires_approval / high|airtable|base_id, table_id|table_id, deleted, http_status|external write; approval/idempotency; precondition checked|
|9|`airtable.list_fields`|List fields in a table|read / low|airtable|base_id, table_id|fields, http_status|none; bounded provider list|
|10|`airtable.get_field`|Retrieve a single field by id|read / low|airtable|base_id, table_id, field_id|field, http_status|none; provider truth; missing/not-found fails|
|11|`airtable.create_field`|Create a new field in a table|write_requires_approval / high|airtable|base_id, table_id, field|field_id, field, changed, http_status|external write; approval/idempotency; provider refusal visible|
|12|`airtable.update_field`|Update a field name, description, or options|write_requires_approval / high|airtable|base_id, table_id, field_id, field; expected_name|field_id, before, after, changed, http_status|external write; approval/idempotency; precondition checked|
|13|`airtable.delete_field`|Delete a field from a table|write_requires_approval / high|airtable|base_id, table_id, field_id|field_id, deleted, http_status|external write; approval/idempotency; precondition checked|
|14|`airtable.list_views`|List views in a table|read / low|airtable|base_id, table_id|views, http_status|none; bounded provider list|
|15|`airtable.get_view`|Retrieve a single view by id|read / low|airtable|base_id, table_id, view_id|view, http_status|none; provider truth; missing/not-found fails|
|16|`airtable.create_view`|Create a new view in a table|write_requires_approval / high|airtable|base_id, table_id, view|view_id, view, changed, http_status|external write; approval/idempotency; provider refusal visible|
|17|`airtable.update_view`|Update a view name|write_requires_approval / high|airtable|base_id, table_id, view_id, name; expected_name|view_id, before, after, changed, http_status|external write; approval/idempotency; precondition checked|
|18|`airtable.delete_view`|Delete a view from a table|write_requires_approval / high|airtable|base_id, table_id, view_id|view_id, deleted, http_status|external write; approval/idempotency; precondition checked|
|19|`airtable.list_records`|List records in a table with optional filtering|read / low|airtable|base_id, table_id; filter_by_formula, view, page_size, max_records, sort, cell_format|records, offset, http_status|none; incremental filtering is deterministic; bounded data is explicit|
|20|`airtable.get_record`|Retrieve a single record by id|read / low|airtable|base_id, table_id, record_id; cell_format|record, http_status|none; provider truth; missing/not-found fails|
|21|`airtable.create_records`|Create up to 10 records with idempotent duplicate guard|write_requires_approval / high|airtable|base_id, table_id, records; typecast, on_conflict|records, created_count, changed, http_status|external write; approval/idempotency; batch limit 10|
|22|`airtable.update_records`|Update records with optimistic precondition|write_requires_approval / high|airtable|base_id, table_id, records; typecast, on_conflict|records, updated_count, changed, http_status|external write; approval/idempotency; precondition checked|
|23|`airtable.delete_records`|Delete records only if their identity still matches|write_requires_approval / high|airtable|base_id, table_id, record_ids|deleted_records, deleted_count, http_status|external write; approval/idempotency; precondition checked|
|24|`airtable.upsert_records`|Upsert records keyed by a field with merge semantics|write_requires_approval / high|airtable|base_id, table_id, records, merge_key; typecast|records, created_count, updated_count, changed, http_status|external write; approval/idempotency; merge key required|
|25|`airtable.search_records`|Search records by cell value across a table|read / low|airtable|base_id, table_id, search; page_size, offset|records, offset, endpoint_used, http_status|none; lookup; invalid/provider error fails closed|
|26|`airtable.list_records_by_view`|List records filtered by a specific view|read / low|airtable|base_id, table_id, view_id; page_size, cell_format|records, offset, http_status|none; bounded provider list|
|27|`airtable.count_records`|Count total records in a table with optional filter|read / low|airtable|base_id, table_id; filter_by_formula|count, http_status|none; composite read; incomplete data is not guessed|
|28|`airtable.list_webhooks`|List webhooks configured for a table|read / low|airtable|base_id, table_id|webhooks, http_status|none; bounded provider list|
|29|`airtable.create_webhook`|Create a webhook for table change notifications|write_requires_approval / high|airtable|base_id, table_id, webhook|webhook_id, webhook, changed, http_status|external write; approval/idempotency; provider refusal visible|
|30|`airtable.delete_webhook`|Delete a webhook from a table|write_requires_approval / high|airtable|base_id, table_id, webhook_id|webhook_id, deleted, http_status|external write; approval/idempotency; precondition checked|
|31|`airtable.enable_webhook`|Re-enable a disabled webhook|write_requires_approval / high|airtable|base_id, table_id, webhook_id|webhook_id, enabled, http_status|external write; approval/idempotency; precondition checked|
|32|`airtable.disable_webhook`|Temporarily disable a webhook without deleting it|write_requires_approval / high|airtable|base_id, table_id, webhook_id|webhook_id, disabled, http_status|external write; approval/idempotency; precondition checked|
|33|`airtable.list_invites`|List pending invitations for a base|read / low|airtable|base_id|invites, http_status|none; bounded provider list|
|34|`airtable.invite_user`|Invite a user to collaborate on a base|write_requires_approval / high|airtable|base_id, email; permission_level|invite_id, invite, changed, http_status|external write; approval/idempotency; email validated|
|35|`airtable.update_invite`|Update permission level for a pending invite|write_requires_approval / high|airtable|base_id, invite_id, permission_level; expected_email|invite_id, before, after, changed, http_status|external write; approval/idempotency; precondition checked|
|36|`airtable.remove_invite`|Revoke a pending invitation|write_requires_approval / high|airtable|base_id, invite_id|invite_id, removed, http_status|external write; approval/idempotency; precondition checked|
|37|`airtable.list_collaborators`|List all collaborators on a base|read / low|airtable|base_id|collaborators, http_status|none; bounded provider list|

For exact types, required flags, and schema descriptions, the manifest is the
authoritative source. Runtime/provider errors are reported in Station receipts;
this reference does not promise a successful provider response.
