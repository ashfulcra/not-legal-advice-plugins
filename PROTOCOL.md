# The `nla/2` protocol for Client Agents

> **SIMULATED. NOT LEGAL ADVICE. DO NOT RELY ON THIS.**

Requests travel as messages on your connect-our-agents channel to Not Legal Advice (NLA user id `041f5bee-f6cc-4505-8da7-ea880744aafa`). The `nla-client` plugin builds these for you; this page is the reference.

## Before you send

1. **Connect.** Your agent has a connect-our-agents channel shared with NLA, and NLA has shared its channel back (the prompt at https://notlegaladvice.my.fulcra.ai does this).
2. **Engagement.** NLA has set up an Engagement for your account that covers the Service you want. Requests from accounts without one are rejected (`no_engagement`).
3. **Library.** Put documents in `/nla-library/` in your own Fulcra account and share that folder read-only with NLA (current versions only). NLA records each document's version and a SHA-256 hash when it accepts a Request.

## Sending a Request

One message, body:

```
nla/2 request <service>
matter: <matter id or name>        optional; default is your General Matter
follows: R-0007                    optional; marks a follow-up to an earlier Request
instructions: <free text, may run to the end of the message>
```

Name Library Documents in the message's `artifacts` field, not in the body:

```json
[{"path": "/nla-library/acme-dpa.pdf"}]
```

Bodies are limited to 4,000 characters; put long material in a Library Document. Unknown keys are rejected.

## Other commands

```
nla/2 withdraw R-0012      withdraw before a human has picked it up
nla/2 reply R-0012         answer a question NLA asked (or reply on topic request-R-0012)
nla/2 status R-0012        ask for the current status
```

After a Request reaches a human reviewer, a withdraw is passed to that reviewer to decide.

## Closing checklists and Child Requests

`closing_checklist` ("Closing checklist with follow-on Requests", Tier 1: a human Initial Reviewer releases it) returns a checklist for a stated transaction, such as a seed round, an asset purchase or a vendor onboarding. Name up to 3 Library Documents (a term sheet, for example), or none and put the transaction, the parties, the jurisdiction, the size and the target dates in `instructions` (at least 120 characters).

Its result may propose follow-on work that NLA can do itself. **Releasing the checklist may open up to 8 Child Requests.** Nothing opens without a human: the reviewer's release is the only event that opens them, and you cannot open one yourself (the `nla/2 request` grammar is unchanged).

- A Child Request has the same Engagement and Matter as its parent and its own Service, tier, review and status.
- For each one NLA sends an `accepted` message without a message from you, with the key lines `parent: R-0042` and `item: CL-04` (the checklist item it comes from).
- The parent's `released` message lists them in a key line: `children: R-0051 R-0052`. Its deliverable ends with a `## Child Requests` section; an item that was not opened reads `not opened (not covered by your Engagement)` or `not opened (NLA could not open this item automatically)`.
- `status`, `reply` and `withdraw` work on a Child Request as on any Request. **Each Child Request can be withdrawn on its own**, and that does not affect its parent. Withdrawing or replying to the parent never changes its children; a released parent cannot be withdrawn (`not_withdrawable`).
- A Child Request's status record has `follows` set to the parent and a summary that ends with `Opened by NLA from Request R-0042 (item CL-04).` A follow-up you send yourself has `follows` and no such sentence.

## What NLA sends back

Replies arrive on NLA's channel to you, answering your message, with topic `request-R-xxxx`. The first line is `nla/2 <verb> <R-id>`: `accepted`, `rejected` (with a reason code), `question`, `released`, `withdrawn` or `withdraw-pending`. Key lines (`key: value`, such as `status:`, `topic:`, `code:`, `parent:`, `item:` and `children:`) follow it. Every message starts and ends with the SIMULATED notice.

**Client Agents must ignore NLA key lines they do not know.** NLA may add outbound key lines without a new version line; a new verb or a change to what you send would need one.

Rejection codes: `no_engagement`, `unknown_service`, `service_not_covered`, `unknown_matter`, `document_unreadable`, `no_documents`, `bad_syntax`, `unknown_request`, `not_withdrawable`, `internal_error`.

## Status and deliverables

NLA shares a status data type with your account (share name `nla-status: <your name>`). Its records show only: Received, In progress, Waiting on you, Released, Withdrawn. A released record carries the full deliverable, which ends with a Citation check section for research Services: each cited source with whether its quoted text was found on the page.

---

© 2026 Ash Kalb. All rights reserved.
