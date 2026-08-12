---
name: Create a Kartra lead and tag it
description: Add a contact to a Kartra account and apply a tag and list subscription in a single API call, handling the create-vs-exists branch correctly.
api: https://app.kartra.com/api
generated: '2026-08-12'
method: generated
source: https://support.kartra.com/en/articles/15369018-lead-searching-creating-editing
operations: [create_lead, assign_tag, subscribe_lead_to_list, search_lead]
---

# Create a Kartra lead and tag it

## What you are talking to

One endpoint. `POST https://app.kartra.com/api`, body `application/x-www-form-urlencoded`.
There are no resource paths and no HTTP verbs beyond POST. The operation is chosen by the
`cmd` value inside the `actions` array.

## Credentials

Every call carries three fields in the body:

- `app_id` — yours, the integration developer's, hard-coded.
- `api_key` — the END USER's, from their account under Settings > Integrations > My API.
- `api_password` — the end user's, issued with the key.

The actions execute in the **App user's** account, not yours. HTTPS is mandatory; plain
HTTP is rejected with `type` 201.

## The call

```
lead[email]                    = person@example.com
lead[first_name]               = John
lead[last_name]                = Smith
lead[custom_fields][0][field_identifier] = text1
lead[custom_fields][0][field_value]      = text message
actions[0][cmd]                = create_lead
actions[1][cmd]                = assign_tag
actions[1][tag]                = <tag name>
actions[2][cmd]                = subscribe_lead_to_list
actions[2][list]               = <list name>
```

## Rules you must not break

1. **`create_lead`, `edit_lead` or `search_lead` must be `actions[0]`.** Kartra resolves
   which lead the rest of the chain applies to from that first command. Put anything else
   first and the chain has no subject.
2. **One lead per call.** You may chain many commands, but they all act on the same lead.
   Adding N contacts costs N calls.
3. **Kartra does not upsert.** `create_lead` on an existing email fails with `type` 244
   ("Lead already exists"). If you do not know whether the contact exists, run `search_lead`
   as `actions[0]` and branch, or accept the 244 and re-issue with `edit_lead` first.
4. **Custom field identifiers must already exist** in the account. An unknown identifier is
   **silently ignored** — not an error. Call `retrieve_custom_fields` first and verify,
   rather than trusting a Success response.

## Reading the response

Do not branch on the HTTP status. Every outcome returns **HTTP 200**; the only documented
non-200 is 429 on rate-limit exhaustion.

```json
{"status": "Success",
 "actions": [{"create_lead": {"status": "Success", "lead_details": {"id": "1234"}}}]}
```

- Check the top-level `status`, case-insensitively.
- Then walk `actions[]` and check each entry's own `status`. A top-level Success does **not**
  mean every chained action succeeded.
- Branch on the numeric `type` field, never on the `message` prose. See
  `errors/kartra-error-codes.yml`.

Relevant codes here: 244 lead already exists, 271 wrong custom field format, 211/212 tag
empty or missing, 207/231 list empty or missing, 262 your App is not approved for this cmd.

## Retries

There is **no idempotency key**. Do not replay a failed write. If a call times out, re-read
with `get_lead` and reconcile before acting again.

## Rate

20 calls per second per App — shared across every user of your App. On 429, wait one second
and retry; put a queue in front of the endpoint.

## Test mode

A new App starts in Test Mode. Kartra sends no email, and every lead's email **domain is
rewritten to @kartra.com** — `johnsmith@gmail.com` is stored as `johnsmith@kartra.com`. Two
test contacts sharing a local-part will collide into one address and the second create will
fail with 244. Vary the local-part in test fixtures, not the domain.
