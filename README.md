# Not Legal Advice: participant plugins

> **SIMULATED. NOT LEGAL ADVICE. DO NOT RELY ON THIS.**
> Not Legal Advice (NLA) is a demonstration of an agent-native law firm built on [Fulcra](https://fulcradynamics.com). Nothing it produces is legal advice, no attorney-client relationship exists, and nobody is charged.

This repository holds what participants need to work with NLA from their own agent:

| Plugin | For | What it does |
|---|---|---|
| `nla-client` | A Client Agent acting for a Client | Connect to NLA, keep a Library of documents, send Requests (`nla/2 request <service>`), read status and released deliverables, answer questions, withdraw |
| `nla-reviewer` | An NLA Initial Reviewer or Signing Attorney | Set up reviewer actions in your own Fulcra account, read your queue, inspect a Request (draft, findings, citation checks) and record decisions |

The dashboard is at https://notlegaladvice.my.fulcra.ai (Requests, Library, review queue). The plugins and the dashboard write the same records.

## Install (Claude Code)

```
/plugin marketplace add ashfulcra/not-legal-advice-plugins
/plugin install nla-client@not-legal-advice
/plugin install nla-reviewer@not-legal-advice
```

The plugins call the Fulcra CLI (`uvx --python 3.11 fulcra-api@latest`) as the signed-in user. Each person and agent acts with their own Fulcra account; NLA never holds your credentials.

## How it works, in one paragraph

A Client Agent sends a Request on its connect-our-agents channel to NLA. NLA's listener accepts or rejects it within a few minutes, a worker agent prepares a draft for the requested Service, NLA mechanically checks every cited source, and a human Initial Reviewer (and, for Tier 2 Services, a Signing Attorney) approves, sends back or signs before anything is released to the Client. The Client only ever sees Received, In progress, Waiting on you, Released or Withdrawn. See [PROTOCOL.md](PROTOCOL.md) for the message format.

## Services

| Service | Tier | Send |
|---|---|---|
| `review_dpa` | 2 | A data processing agreement from your Library |
| `review_clause` | 1 | A Library Document naming the clause, or the clause text in the instructions |
| `approve_vendor_terms` | 2 | Vendor terms of service, MSA or order form |
| `answer_regulatory_question` | 1 | The question, jurisdiction and facts in the instructions |
| `issue_opinion` | 2 | The opinion question, addressee and facts in the instructions |
| `form_entity` | 2 | Business purpose, founders, preferred state, tax and investor plans |
| `file_annual_report` | 1 | Entity name, state, type, fiscal year and share or asset figures |
| `closing_checklist` | 1 | The transaction, parties, jurisdiction and dates; release may open up to 8 follow-on Requests, each withdrawable |

Tier 1: an Initial Reviewer reviews and releases. Tier 2: an Initial Reviewer reviews and a Signing Attorney signs. All simulated.

---

© 2026 Ash Kalb. All rights reserved. Published so participants can use these plugins with Not Legal Advice; no other licence is granted.
