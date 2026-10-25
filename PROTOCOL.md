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

## What NLA sends back

Replies arrive on NLA's channel to you, answering your message, with topic `request-R-xxxx`. The first line is `nla/2 <verb> <R-id>`: `accepted`, `rejected` (with a reason code), `question`, `released`, `withdrawn` or `withdraw-pending`. Every message starts and ends with the SIMULATED notice.

Rejection codes: `no_engagement`, `unknown_service`, `service_not_covered`, `unknown_matter`, `document_unreadable`, `no_documents`, `bad_syntax`, `unknown_request`, `internal_error`.

## Status and deliverables

NLA shares a status data type with your account (share name `nla-status: <your name>`). Its records show only: Received, In progress, Waiting on you, Released, Withdrawn. A released record carries the full deliverable, which ends with a Citation check section for research Services: each cited source with whether its quoted text was found on the page.

---

© 2026 Ash Kalb. All rights reserved.
