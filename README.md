# Kartra

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Kartra is an all-in-one online business platform operated by Kartra, Inc. of Las Vegas, Nevada, that bundles landing pages and funnels, email marketing with automation sequences, checkouts and recurring billing, membership sites and courses, video hosting, calendars and booking, helpdesks, affiliate management and lead scoring into a single subscription. Its developer surface is three distinct things: an inbound API that is a single form-encoded POST endpoint at https://app.kartra.com/api driven by 29 documented `cmd` values and scoped to exactly one lead per call; an outbound API that POSTs 24 JSON lead and commerce events to URLs configured in the account; and an IPN system that POSTs six payment-lifecycle notifications as flat form variables. Kartra publishes no OpenAPI, no SDK in any language, no signed webhooks and no idempotency contract, and it returns HTTP 200 for every outcome including authentication failure — the numeric `type` field in the response body is the real error signal.

**Areas:** Company · Marketing Automation · Email Marketing · CRM · Contacts · E-Commerce · Payments · Subscription · Membership · Landing Pages · Webhook · Software-as-a-Service

## Kartra on APIs.io

Everything profiled for Kartra is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/kartra/](https://apis.io/providers/kartra/)** — or go straight to one of the APIs or properties below.

### APIs (1)

- [Kartra API](https://apis.io/apis/kartra/kartra-api/) — Kartra's inbound developer API. One endpoint, POST only, form-encoded, with the operation selected by `actions[].cmd`. Twenty-nine documented commands cover…

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### AsyncAPI (1)

Event-driven and webhook contracts.

- [Kartra Webhooks](https://apis.io/asyncapis/kartra/kartra-webhooks/)

#### MCP servers (1)

Model Context Protocol servers that expose the API to agents.

- [kartra-mcp.yml](https://apis.io/servers/kartra/kartra-mcp-yml/)

#### Security (3)

Authentication, trust centre, and vulnerability disclosure.

- [Kartra Authentication](https://apis.io/security/kartra/kartra-authentication/)
- [Kartra Domain Security](https://apis.io/security/kartra/kartra-domain-security/)
- [Kartra Vulnerability Disclosure](https://apis.io/security/kartra/kartra-vulnerability-disclosure/)

#### Plans and pricing (1)

Published plans, tiers and prices.

- [Kartra Plans Pricing](https://apis.io/plans/kartra/kartra-plans-pricing/)

#### Rate limits (1)

Published request limits.

- [Kartra Rate Limits](https://apis.io/rate-limits/kartra/kartra-rate-limits/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://kartra.com/)
- [Documentation](https://support.kartra.com/en/collections/19655232-developers)
- [Getting started](https://support.kartra.com/en/articles/15369011-activating-your-app)
- [API reference](https://support.kartra.com/en/articles/15369013-connecting-to-the-api)
- [Pricing](https://kartra.com/pricing/)
- [Sign up](https://kartra.com/kartra-trial/)
- [Support](https://support.kartra.com/en/)
- [Blog](https://kartra.com/learning-center/)
- [Compliance](https://kartra.com/gdpr/)
- [Terms of service](https://kartra.com/terms-conditions/)
- [Privacy policy](https://kartra.com/privacy-policy/)
- [Status](https://status.kartra.com/)

## API Evangelist rating

**Kin Score 60.0** — strong · rubric 0.25.0 · scored 2026-10-23

| Facet | Score |
|---|---|
| Discoverability | 66.1 |
| Contract quality | 39.0 |
| Contract governance | 18.2 |
| Operational transparency | 71.1 |
| Developer ergonomics | 66.1 |
| Access clarity | 84.2 |

**Agent readiness 26.1** — agent-aware

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | yes |
| Agentic Access | — |
| Reversibility Documented | — |
| MCP Server | — |
| Auth Clarity | bearer (partial credit) |
| Idempotency | — |
| Error Semantics | documented (partial credit) |
| OpenAPI Examples | — |
| Rate Limit Signal | documented (partial credit) |
| Event Surface Described | yes |
| Agent Skills | derived (partial credit) |
| Well Known Catalog | — |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | — |
| Delegated Identity | — |
| Protected Resource Metadata | — |
| Dynamic Client Registration | — |
| Agentic Commerce | — |
| Pagination Ratio | na |
| Operation Distinctiveness | na |

**Regulatory alignment 33.9** — Payments

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/kartra/](https://apis.io/providers/kartra/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for Kartra: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/kartra/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
