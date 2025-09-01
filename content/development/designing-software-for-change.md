---
title: "Designing Software for Change: Why Modularity and Boundaries Beat Big Rewrites"
date: 2025-07-31
summary: "Rewrites look tempting but rarely fix the root cause. The real answer is software built for change: modular boundaries, stable contracts, and evolution without big bangs."
description: "Why the best software isn’t the one that’s perfect today, but the one that can keep evolving tomorrow."
categories: ["Development"]
tags: ["development", "modularity", "software architecture", "boundaries", "clean architecture", "domain-driven design", "technical debt", "engineering leadership"]
cover:
  image: "/images/covers/development/designing-software-for-change-hero.png"
  alt: "Landing zone reference shape with guardrails and paved road"
  relative: false
showtoc: true
tocopen: true
author: "Tarik Miri"
---

Change isn’t an edge case in software; it’s the workload. Product strategy pivots, pricing models evolve, regulations arrive, dependencies deprecate, traffic patterns shift, teams rotate. Most codebases, however, are assembled as if the world will stay still. When reality intrudes, the reflex is predictable: **“let’s rewrite it.”** Rewrites feel relieving, but they’re slow, risky, and usually repeat the same structural mistakes under a shinier framework.

A better approach is to **treat evolvability as a first-class requirement** and design for it from day one. That doesn’t mean chasing clever frameworks or silver bullets. It means engineering for **replaceability and containment**:

- **Modularity**: slices of the system that can change, be tested, and be deployed without rippling through everything else.  
- **Strong boundaries**: explicit, versioned contracts (APIs/events/schemas) with owned data and clear responsibility lines.

Systems built this way aren’t “future-proof” (nothing is), but they are **future-ready**: you swap an implementation without renegotiating the whole architecture, add capability without a freeze, and absorb technology churn without derailing delivery. In practice, that translates into faster change with smaller blast radii, simpler testing, safer rollouts,  and far fewer conversations about starting over.

This article explores how modularity and strong boundaries enable software systems to evolve safely and sustainably, avoiding the costly rewrite trap while keeping pace with constant business and technology change.

---

## The Rewrite Trap

Rewrites almost always begin with sensible frustrations. The code is tangled, the framework is past end-of-life, and onboarding a new developer feels like handing them a crossword with half the clues missing. From the inside, a clean slate promises relief: all the compromises paid back at once, all the “we’ll fix it later” IOUs finally cleared. It feels liberating.

The reality is harsher. A rewrite imposes a feature freeze, explicitly or implicitly, because every hour spent on the greenfield is an hour not spent improving the product your customers are using today. That pause accumulates interest: competitors ship, sales promises stall, and product managers queue “must-haves” that cannot land until the big switch. Meanwhile, your new code does not get a free pass on physics. First versions re-learn edge cases the hard way, old bugs reincarnate under new names, and your test suite, which knew how to catch yesterday’s failures, no longer maps neatly to the new world.

There is also the integration tax. Systems rarely exist alone; they speak to payment processors, identity providers, reporting pipelines, warehouses, and a dozen internal services with their own quirks. The rewrite plan often budgets for “parity,” but parity depends on implicit contracts the old system honored by accident: timestamp semantics, idempotency guarantees, and error shapes that downstream jobs quietly rely on. You do not discover those until late, when the “nearly done” rewrite meets production-shaped reality.

Morale takes a hit in surprising ways. Early on, the team celebrates velocity: new files, new patterns, new CI passing. Six months later, the dopamine fades, and the work feels like a museum restoration, painstaking, invisible, and perpetually two exhibits from completion. Engineers who joined to build new capability watch themselves re-implementing old behavior. Leadership, under pressure from the freeze, begins to ask for shortcuts. That pressure is how reliability regressions escape.

Underneath all of this sits the root cause: missing boundaries. Codebases rot not because a particular framework aged out, but because unrelated concerns bled into each other until change could not be local. Business logic knew about database schemas, HTTP handlers knew about queue topology, analytics reached directly into operational stores. In a system without strong seams, any change becomes a cross-cutting change. If you carry that structure into a new codebase, the decay pattern simply restarts with a different syntax highlighting theme.

A useful diagnostic is to ask: what, precisely, are we trying to change, and where is the seam that would let us swap it safely? If you can point to a contract such as an API, an event schema, or a well-defined adapter, then a rewrite is rarely necessary; you can evolve in place behind that boundary. If you cannot name the seam, a ground-up rewrite will not conjure one for free; you will need to design and enforce it anyway, which is the real work.

This does not mean rewrites are never justified. Catastrophic security posture, architectural dead ends such as a single-tenant design that cannot be made multi-tenant without violating isolation, or platform deprecations that eliminate critical guarantees can mandate a fresh foundation. But those are exceptions, and even then, successful efforts start by carving boundaries first: introducing anti-corruption layers, defining versioned contracts, and incrementally routing traffic using a strangler approach so you de-risk in slices rather than betting the company on a big-bang cutover.

In short, the rewrite trap is a seductively simple solution to a boundary problem. Without contracts and containment, new code inherits the same fragility as the old. With them, most “we need a rewrite” sentiments degrade into a tractable sequence of local changes, fewer heroics, and more reliability.

---

## Modularity as a Survival Strategy

Modularity is not about slicing code into small files or creating a maze of micro-packages. It is about the ability to change one part of a system without forcing unintended changes in others. In practice, this means designing with separation of concerns, clear contracts, and stable seams that absorb change rather than propagate it. When a system is modular, the impact of a modification is contained. When it is not, even trivial adjustments ripple across the codebase and create an outsized cost of change.

The benefits are tangible. A modular service can evolve independently without introducing unpredictable side effects elsewhere. Tests are faster to write and more reliable because each module exposes narrow, well-defined inputs and outputs. Deployment pipelines become safer since they can validate and ship isolated components without rebuilding or retesting the entire estate. Teams also scale better in modular systems: two groups can work in parallel on different modules without stepping on each other’s toes, because the seams protect their autonomy.

Several approaches have proven effective at creating this kind of resilience:

- **Domain-Driven Design (DDD):** encourages the definition of bounded contexts, where each part of the domain owns its data and logic explicitly. This prevents accidental coupling and keeps changes local to the context that owns them.  

- **Hexagonal / Clean Architecture:** applies the principle of ports and adapters to isolate business rules from infrastructure concerns. External technologies, such as databases, messaging systems, or frameworks, can evolve without destabilising the domain logic at the centre.  

- **Microservices:** separate systems at the deployment boundary by assigning ownership to business capabilities rather than technical layers. This enables teams to deploy and scale independently. However, microservices are not synonymous with modularity. Without disciplined contracts and boundaries, a cluster of microservices can become just as entangled and fragile as a monolith.  

In the end, modularity is less a design choice than a survival strategy. Businesses shift direction, teams grow and shrink, and technologies rise and fall. Systems that lack modularity ossify under the weight of change, becoming harder to evolve with each release. Systems that embrace it continue to adapt, because their boundaries allow change to be absorbed locally instead of infecting the whole.

---

## Boundaries in Practice

A boundary in software is not a directory in a repository or a naming convention. It is a **contract** that separates what is internal from what is external. Good boundaries allow a system to evolve because they make change predictable: teams know what is guaranteed to remain stable and what can change freely behind the seam.

### What makes a good boundary?

- **Clear contracts.** A boundary should be defined by explicit APIs, events, or schemas that describe how other modules can interact with it. Everything else is hidden and subject to change without notice.  
- **Minimal dependencies.** The fewer assumptions a boundary makes about the systems around it, the easier it is to adapt. A component tightly coupled to another’s database schema or internal types is not a boundary; it is a leak.  
- **Explicit ownership.** Someone must be accountable for the boundary. When ownership is diffuse, contracts erode, shortcuts creep in, and the boundary collapses. Clear responsibility ensures consistency and evolution.  

To make boundaries concrete, consider two scenarios that most systems face in practice.

1. **Payment Service**: The order system does not need to know whether payments are processed through Stripe, PayPal, or a direct bank integration. The only contract it relies on is simple: given an order, return a payment status. As long as that contract holds, the payment service can evolve its internals, switch providers, or add fraud checks without forcing changes across the wider system.

2. **Logging and Observability**: Every module emits logs through the same interface, regardless of the underlying platform. Whether those logs are shipped to ELK, Datadog, or a cloud-native observability stack is an implementation detail hidden behind the boundary. This decouples teams from vendor churn and prevents costly rewrites whenever the organisation switches providers.

Strong boundaries are the antidote to erosion. They turn change into a local event, not a system-wide negotiation. Without them, modularity is an illusion; with them, systems remain adaptable long after the initial architecture diagram has been forgotten.

---

## Evolving Without Big Bangs

Change in software does not have to mean starting over. Large, all-or-nothing rewrites are the most expensive way to adapt a system. Mature teams favour incremental evolution, where new behaviour is introduced gradually and old behaviour is retired in controlled slices. This approach reduces risk, keeps delivery flowing, and allows learning along the way instead of betting everything on a single cutover.

One proven technique is the **Strangler Pattern**. Instead of replacing a system in one move, you wrap existing functionality and route new flows to the replacement piece by piece. Over time, the old code is strangled out of existence without a dramatic “day one” switch. Amazon famously used this approach when breaking apart their monolithic retail platform. Rather than rewriting everything, they incrementally extracted business capabilities into services, fronted them with clear APIs, and redirected traffic one path at a time. The result was evolution at scale without halting delivery for years.

Another is the use of **feature flags and canary releases**. By controlling rollout at runtime, teams can expose new behaviour to a subset of users, measure impact, and roll back instantly if needed. Netflix relies heavily on this model: a new recommendation algorithm might be trialled with a few percent of the user base before being rolled out globally. Failures are contained, and learnings arrive early.

Finally, **pipelines that enforce boundaries** ensure that evolution is disciplined. Continuous integration and delivery do more than ship code; they validate that contracts between modules are respected, that dependencies remain within policy, and that changes are observable from the start. In this way, the delivery pipeline itself becomes a guardian of system integrity.

Think back to the idea of landing zones. The value is not in a particular implementation but in the contract it guarantees. The same principle applies here: when contracts are stable, implementations can change freely. That is how systems evolve without the drama of a big bang.

---
## Leadership Perspective

Boundaries are never just a technical concern. They are organisational by nature, because the way software is structured inevitably reflects the way teams communicate and collaborate. This is Conway’s Law in action: systems mirror the structures of the organisations that build them. If teams are fragmented, ownership is unclear, or responsibilities overlap, the code will show the same symptoms. Conversely, strong boundaries in code usually reflect clear ownership and healthy collaboration patterns in the organisation.

For distributed teams, especially those spread across time zones, modularity is what makes autonomy possible. A well-defined boundary means a team in London can evolve a module without waiting for sign-off from a team in Singapore, as long as the contract remains intact. Workflows stay decoupled, releases move faster, and coordination overhead is dramatically reduced.

The role of leadership is not to micromanage implementation details but to create the conditions where boundaries hold. That means setting and enforcing standards for APIs, schemas, and contracts. It means investing in paved paths—frameworks, templates, and tooling that make the right way also the easiest way. And it means holding teams accountable for respecting contracts, so local optimisations do not erode global consistency.

When the rules of collaboration are embedded in both the organisation and the architecture, teams thrive. They spend less time negotiating interfaces, less time fixing unintended ripple effects, and more time delivering value. Strong technical boundaries reinforce strong organisational boundaries, and together they create a system that can scale in people, in code, and in change.

---
## Closing Thoughts

The temptation to rewrite is strong, but it is usually a symptom of systems that were never designed to accommodate change. Starting over feels like a shortcut, yet in practice it only delays the inevitable: unless the underlying structure improves, the new codebase will decay in the same way as the old one.

The sustainable path is to build with **modularity and boundaries from the very beginning**. Boundaries make change safe because they localise its impact. Modularity allows teams and systems to scale without collapsing into chaos. Contracts give evolution predictability, ensuring that today’s improvements do not become tomorrow’s surprises.

If a codebase cannot change safely, it is already legacy, no matter how modern the syntax or how new the framework. The real mark of quality software is not how elegant it looks on day one but how gracefully it adapts on day one thousand. **Design for change, and rewrites will be the rare exception rather than the recurring plan.**

---
