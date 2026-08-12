---
name: Read and update a Kartra contact
description: Fetch a lead's full profile from Kartra, including GDPR consent state and custom fields, then write changes back safely.
api: https://app.kartra.com/api
generated: '2026-08-12'
method: generated
source: https://support.kartra.com/en/articles/15369017-lead-retrieving-data
operations: [get_lead, search_lead, edit_lead, retrieve_custom_fields, retrieve_account_tags, retrieve_account_lists]
---

# Read and update a Kartra contact

## Read

`get_lead` is a **top-level request key**, not an `actions[]` command:

```
app_id       = ...
api_key      = ...
api_password = ...
get_lead[email] = person@example.com
```

The response returns the whole profile — there is no sparse-fieldset or expansion
parameter, and **every custom field comes back even when empty**.

Errors: 234 no lead found, 245 lead is deleted, 236 `get_lead` not an array.

## What comes back that matters

- **Identity** — `id` (Kartra's key) and `email` (the natural key). If you send both on a
  later write, `id` wins.
- **`source`** — how the contact entered: `single-optin`, `double-optin`, `helpdesk`,
  `affiliate-signup`, `checkout`, `import`, `api`, `manual`.
- **`blacklisted`** — 0 or 1. Check this before any send decision.
- **GDPR consent, machine-readable.** `gdpr_lead_status` (0 GDPR off, 1 not subject,
  2 accepted, 3 not accepted, 4 unknown, 5 pending), plus `gdpr_lead_status_date`,
  `gdpr_lead_status_ip` and `gdpr_lead_communications` (0/1, agreed to be contacted).
  **Treat 3 and 0/1 differently** — "not accepted" is a refusal; "not subject" is not.
- **Relations** — assigned tags, list subscriptions (each with an `active` flag rather than
  removal), sequence subscriptions with `step` and `status`, and membership levels.
- **Timestamps** are `YYYY-MM-DD HH:MM:SS` in **EST with no offset**. Localise them
  yourself; they are naive strings.

## Write

`edit_lead` must be `actions[0]`:

```
lead[email]      = person@example.com
actions[0][cmd]  = edit_lead
lead[city]       = Portland
lead[custom_fields][0][field_identifier] = dropdown1
lead[custom_fields][0][field_value]      = 612
```

- Custom field value typing follows `field_type`: string for `input_field` / `text_area`,
  an option id for `drop_down` / `radio_button`, an **array** of option ids for `checkbox`.
- **Send an empty string to clear a value.**
- An unknown `field_identifier` is silently ignored. Resolve identifiers with
  `retrieve_custom_fields` before writing.

## Discovery helpers

`retrieve_custom_fields`, `retrieve_account_tags`, `retrieve_account_lists`,
`retrieve_account_sequences` and `retrieve_account_pages` return the account's full
inventory. **None of them paginate** — no page, limit, offset or cursor exists — so on a
large account expect a single unbounded response.

## Response handling

HTTP 200 always. Read `status` from the body, then each entry in `actions[]`. Branch on the
numeric `type`, never on `message`. Full registry: `errors/kartra-error-codes.yml`.

## Safety

No idempotency key exists. `edit_lead` is a blind overwrite of the fields you send — read
first, merge yourself, then write, and never replay a timed-out write without re-reading.
