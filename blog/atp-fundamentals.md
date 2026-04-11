title: Three Things the Travel Industry Must Get Right Before AI Agents Can Work
description: Interoperability, the Booking Object, and bounded AI agent authority — the infrastructure the industry needs before agentic travel works.
author: Tom Sato
date: 2026-04-12
draft: true

# Three Things the Travel Industry Must Get Right Before AI Agents Can Work

*By Tom Sato, Founding Chair, Activity Travel Protocol Foundation | April 2026*

---

Everyone in travel is talking about AI agents. Expedia has Romie. Google is building agentic booking into Search. Booking.com, Klook, GetYourGuide — all running experiments. The question being asked everywhere is *when* AI agents will transform travel booking.

The question nobody is asking — and the one that actually matters — is *what infrastructure has to be in place first*.

I've spent the last year building that infrastructure from the ground up, starting from a real operational problem at a boutique accommodation property in the Japanese Alps. What I found is that the travel industry has three foundational problems to solve before AI agents can do anything more than search and book. Everything else — the natural language interfaces, the personalisation, the autonomous rebooking — depends on getting these three things right.

---

## Problem One: Nobody Can Talk to Each Other

When a traveller books a hotel through Booking.com, a farm experience through Airbnb Experiences, and a transfer through a local DMC, three separate systems confirm three separate transactions. Those systems have never met. They share no common language, no common trust model, and no common event model.

This is the interoperability problem. And it is not primarily a technology problem — it is a *data model* problem. The travel industry has no shared object that can hold accommodation, activity, transport, participant data, and payment state together across multiple suppliers simultaneously.

The result is visible every time a flight is delayed. The hotel doesn't know. The activity supplier doesn't know. The transfer driver doesn't know. The guest discovers the problem themselves and makes three phone calls — or, increasingly, sits in three separate customer service chat queues while an AI agent tells them it can't help because it only has access to one of the three bookings.

**Interoperability in travel requires a shared protocol — not just shared APIs.** An API tells you how to call a system. A protocol tells you how multiple systems coordinate around a shared object, enforce shared policies, and propagate shared events. The travel industry has thousands of APIs. It has no shared protocol.

The Activity Travel Protocol is designed to be that protocol. It defines a common language for how suppliers, operators, AI agents, and guests interact around a booking — from the first inquiry through journey completion. It is published under Apache 2.0. Any party can implement it. No single company controls it.

---

## Problem Two: The Booking Confirmation Is the End of the System's Awareness

Here is the structural problem with every OTA and booking platform operating today: their data model ends at confirmation.

A booking ID points to a confirmed transaction. It does not hold:

- The guest's live journey state — where are they right now?
- Cross-supplier coordination state — who knows about the delay?
- Participant-level data — dietary requirements, physical needs, skill level, safety acknowledgements
- Disruption orchestration logic — what happens when one component changes?
- AI agent participation with bounded authority — what is the agent permitted to do, and how is that enforced?

The reservation record was designed for a world where confirmation was the finish line. The guest gets a confirmation email, the supplier gets a booking notification, and the system goes quiet until something goes wrong.

In a world of AI agents and complex multi-supplier journeys, confirmation is not the finish line. It is the starting gun.

**What is needed is a Booking Object — a live runtime entity, not a transaction record.**

The Activity Travel Protocol's Booking Object holds the full journey lifecycle as first-class state. It travels with the guest from inquiry through completion. Every supplier in the journey — hotel, activity, transfer, guide — is connected to the same Booking Object through a shared trust chain. Every state transition — check-in, activity start, disruption, completion — is recorded in an immutable event log.

The Booking Object has two axes:

**Axis 1 — The booking lifecycle state machine.** INQUIRY → CONFIRMATION → PRE_JOURNEY → IN_JOURNEY → COMPLETION. Each transition is guarded — an invalid transition is not possible. Prices are snapshotted at CONFIRMATION on the server side, not computed client-side. Participant records are structured per-person from the start, not collapsed into headcounts.

**Axis 2 — The eight IN_JOURNEY phases.** From ASSEMBLY_POINT (meeting the guest at the station) through GUIDED_EXPERIENCE, ACCOMMODATION, FREE_TIME, TRANSFER, and through to DEPARTURE. Each phase has its own duty of care state, supplier assignments, and coordination events.

This dual-axis model is what enables a journey — not just a booking. The accommodation is in the same Booking Object as the activity, the transfer, and the guide. When the flight delays, the Booking Object knows. Every connected supplier knows. The AI agent knows — and can act within its mandate.

---

## Problem Three: AI Agents Have No Bounded Authority — and No Audit Trail

Here is the trust problem that the entire industry is circling around right now without naming precisely.

An AI agent that can book travel can also make mistakes. It can rebook the wrong flight. It can cancel an activity the guest still wanted. It can spend money outside the guest's budget. In a B2B context, it can violate corporate travel policy. In any context, it can act without a record that satisfies regulators or finance teams.

The question "should we deploy AI agents for travel?" is actually the question "how do we deploy AI agents whose authority is bounded, auditable, and revocable?" Nobody in the industry has a complete answer to that question yet. The approaches being tried — rate limits, content filters, human confirmation screens — are application-level patches on a protocol-level problem.

**The Activity Travel Protocol solves this at the protocol layer through the ATP Mandate Model.**

Every AI agent that interacts with a Booking Object holds a cryptographically signed mandate — an Ed25519 JWT that declares exactly what the agent is authorised to do, scoped to a specific Booking Object UUID. The mandate uses Cedar policy for partial evaluation: the agent's authority is checked before any action, re-evaluated on every state transition (the Windley Loop), and can be narrowed but never expanded.

The result: the agent can rebook within policy without human review. It escalates outside policy — automatically, to a human operator — via the Human Escalation Manager (24 defined scenarios where a human must decide). Every action is logged in the Booking Object's immutable event log. The audit trail exists by design, not as an afterthought.

This is not an AI safety feature bolted onto a booking system. It is the architecture of trust for an AI-native travel protocol.

---

## What This Looks Like in Practice

These three components — interoperability protocol, Booking Object, bounded AI agent — are not theoretical. They are implemented and published.

The Activity Travel Protocol Foundation has published all four layers of the protocol at [activitytravel.pro](https://activitytravel.pro): identity and trust, capability discovery, booking workflow, and schema and SDK. The MCP Server specification defines eight tools, OAuth 2.1 authentication, and the mandate model. The SDK is twelve TypeScript packages under Apache 2.0. The llms.txt is live, making the protocol machine-readable to any AI agent or coding assistant.

The reference implementation is running on a real operational system: MyAuberge K.K., a boutique accommodation property in Chino, Nagano, Japan, with Ponyhouse Farm as the first independent activity supplier operating under the protocol's trust model.

The path from the current fragmented state of travel technology to a world of reliable, trustworthy AI agents is not primarily an AI problem. It is an infrastructure problem — and the three components above are the infrastructure.

---

## Where to Go From Here

If you are a travel technology professional, product leader at an OTA or aggregator, or an AI engineer building for travel, the Activity Travel Protocol is designed for you. The full specification is at [activitytravel.pro](https://activitytravel.pro). The protocol is Apache 2.0 — read it, implement it, contribute to it.

If you want to understand the industry context that shaped the design, the blog at [activitytravel.org/blog](https://activitytravel.org/blog) covers the journey in depth:

- Why the travel industry's fragmentation is a data model problem, not a distribution problem
- How the Booking Object solves what OTAs cannot
- What AI agent authority in travel actually requires — technically and legally
- The regulatory landscape across five jurisdictions and what it means for protocol design
- The OCTO Bridge and how existing aggregator inventory connects to the protocol

The Activity Travel Protocol Foundation (in formation / 設立準備中) is an independent standards body governed as a 一般社団法人 under Japanese law. MyAuberge K.K. is the Founding Member. No single commercial entity controls the standard.

The competitive window for early adoption is open. The industry is asking exactly the right questions. The infrastructure exists.

---

*Tom Sato is CEO of MyAuberge K.K. and Founding Chair of the Activity Travel Protocol Foundation. He is a former Microsoft Windows/SDK Product Manager. The Activity Travel Protocol is published at [activitytravel.pro](https://activitytravel.pro) under Apache 2.0.*
