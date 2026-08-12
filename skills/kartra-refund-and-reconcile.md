---
name: Refund a Kartra transaction and reconcile
description: Find a customer's transactions and subscriptions in Kartra, inspect one, and issue a refund or cancellation with the failure modes handled.
api: https://app.kartra.com/api
generated: '2026-08-12'
method: generated
source: https://support.kartra.com/en/articles/15369029-action-refund-a-payment-transaction
operations: [retrieve_transactions_from_lead, get_transaction_details, search_transaction, refund_transaction, retrieve_subscriptions_from_lead, get_subscription_details, cancel_transaction, modify_subscription_status, edit_subscription]
---

# Refund a Kartra transaction and reconcile

> **These commands move money and change billing state. None of them are idempotent, and
> every one of them returns HTTP 200 whether it worked or not.** Read the response body.

## Find it first

Transactions are only reachable **through a lead** — there is no account-wide transaction
query. Every call still needs `lead[email]` or `lead[id]` plus the three credentials.

1. `retrieve_transactions_from_lead` — all payments for the contact.
2. `retrieve_subscriptions_from_lead` — all recurring agreements for the contact.
3. `search_transaction` / `search_subscription` — locate a specific one.
4. `get_transaction_details` / `get_subscription_details` — full detail before you act.

Inspect `transaction_type` (`sale`, `refund`, `partial_refund`) and `transaction_parent_id`
before refunding — a reversal already points at its original sale, and refunding a refund is
not a thing.

## Refund

```
lead[email]     = person@example.com
actions[0][cmd] = search_lead
actions[1][cmd] = refund_transaction
actions[1][transaction_id] = <id>
```

Failure modes you must handle, by `type`:

| type | meaning | what to do |
|---|---|---|
| 247 | Transaction doesn't exist | wrong id, or it belongs to another lead |
| 250 | Transaction outside refund period | terminal — do not retry |
| 251 | Transaction already refunded | terminal — treat as success-equivalent |
| 253 | This type of transaction cannot be refunded or cancelled | terminal |
| 268 | Invalid amount | partial refund amount is out of range |
| 266 | Refund has failed | gateway-side failure — **re-read before retrying** |

## Cancel a subscription

`cancel_transaction` cancels a recurring subscription. `modify_subscription_status` changes
its status; `edit_subscription` changes quantity, price, recurring period, installments or
tax.

| type | meaning |
|---|---|
| 248 | Subscription doesn't exist |
| 249 | Transaction not linked to any recurring subscription |
| 252 | Subscription already cancelled |
| 267 | Cancellation has failed |
| 274 | Wrong status |
| 275 | Subscriptions cancelled or terminated cannot be updated |
| 284 | PayPal transactions cannot be modified |
| 276 / 277 / 282 / 290 | Quantity 1–9,999 / price 1.00–9,999.99 / installment count / tax 0–100 out of range |

**284 is the one to plan for**: whether an edit is possible at all depends on which gateway
processed the original charge. Kartra does not process cards itself — Stripe,
Authorize.net, PayPal, Braintree and Square sit behind it — so capability varies per
transaction and you cannot know it from the subscription record alone.

## Reconcile

`refund_transaction` returning 266 ("Refund has failed") is ambiguous — it does not tell you
whether the gateway moved money. **Do not retry.** Re-read with
`retrieve_transactions_from_lead` and look for a new `refund` / `partial_refund` row whose
`transaction_parent_id` is the original id. Only act again if none appears.

The same reasoning applies everywhere on this API: there is no idempotency key, no request
id and no dedupe window, so the only safe recovery from an uncertain write is a read.

## Cross-check against the event stream

Kartra also fires an outbound `refund_product` / `partial_refund_product` event and an IPN
callback on the same transaction. Neither is signed, so treat them as hints and confirm
against the API. See `asyncapi/kartra-webhooks.yml`.
