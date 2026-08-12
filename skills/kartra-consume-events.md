---
name: Consume Kartra events safely
description: Receive Kartra's outbound API webhooks and IPN payment callbacks, with the verification, N/A and duplication caveats made explicit.
api: https://app.kartra.com/api
generated: '2026-08-12'
method: generated
source: https://support.kartra.com/en/articles/15369054-activating-the-outbound-api
operations: []
---

# Consume Kartra events safely

Kartra has **two** separate, overlapping callback surfaces. Know which one you are wired to.

| | Outbound API | IPN |
|---|---|---|
| Encoding | raw body, **JSON** | flat **form POST** variables |
| Configured at | Integrations > My API > Outbound API | per product, IPN URL |
| Envelope | `{lead, action, action_details}` | flat `transaction_*`, `product_*`, `lead_*` fields |
| Covers | 24 lead/behaviour/commerce events | 6 payment lifecycle notifications |

## Verification — read this first

**Kartra signs nothing.** There is no HMAC, no shared secret, no timestamp header, no
documented source-IP range, and no replay protection on either surface. Kartra's own sample
consumer is a bare `file_get_contents("php://input")`.

Consequences you must design around:

- Anyone who learns your endpoint URL can forge a payload. Use an unguessable path, and
  **treat every event as an untrusted hint** — re-read the authoritative state through the
  inbound API before acting on anything that matters (money, access grants, revocations).
- No retry or ordering semantics are published, so you can neither assume at-least-once nor
  rule out duplicates. Make your handler idempotent on your side; Kartra gives you nothing
  to dedupe on.

## Outbound API envelope

```json
{ "lead": { ...full lead profile, identical on every event... },
  "action": "assign_tag",
  "action_details": { "tag": { "tag_id": 1, "tag_name": "..." } } }
```

The `lead` object is the same shape on all 24 events, including `custom_fields[]` and the
four `gdpr_*` consent fields. `action_details` is what varies. Switch on `action`.

Commerce events: `buy_product`, `refund_product` **or** `partial_refund_product` (one event,
two possible values — handle both), `cancel_subscription`, `affiliate_signup`, `jv_signup`.

Lifecycle events: `assign_tag`, `unassign_tag`, `list_subscription`, `list_unsubscription`,
`sequence_subscription`, `sequence_unsubscription`, `sequence_completion`,
`membership_granted`, `membership_revoked`, `filled_form`, `reaches_score`,
`calendar_subscribe`, `calendar_cancel`, `calendar_complete`, `survey_complete`,
`video_play`, `video_complete`, `kartra_page_visit`, `click_page_split_test_link`.

Full per-event field lists: `asyncapi/kartra-webhooks.yml`.

## IPN quirks

- **Empty values arrive as the literal string `"N/A"`**, not null and not an absent key.
  String-compare; do not test for falsiness.
- `transaction_type` is the discriminator: `sale`, `refund`, `partial_refund`.
- On a reversal, `transaction_parent_id` carries the **original sale's order id** — that is
  your join key back to the sale.
- Timestamps are `YYYY-MM-DD HH:MM:SS` in **EST with no offset**.

## The overlap trap

A single purchase can reach you **twice** — once as an outbound `buy_product` JSON event and
once as an IPN form POST — with different encodings and different field names for the same
facts. If both are configured, deduplicate on `transaction_id` and pick one surface as
authoritative rather than processing both.

## Test mode

While your App is in Test Mode, no email is sent and lead email domains are rewritten to
`@kartra.com`, so events you generate for testing carry rewritten addresses. There is no
event replay or trigger tool — the only way to produce a callback is to perform the real
action in the account.
