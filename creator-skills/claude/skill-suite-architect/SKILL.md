---
name: skill-suite-architect
description: >-
  Designs and builds a coordinated suite of multiple Claude Skills for a complex domain that genuinely needs several specialized skills working as one system, not a single skill. Use whenever the request is for a whole family or set of skills for an organization, role, or function with multiple distinct sub-roles — e.g. a CEO/CTO/CFO leadership skill set, an executive advisory skill system, skills for each department that must work together without stepping on each other, specialized skills for different functions that risk overlapping, or a whole skill ecosystem for a domain rather than just one skill. The signal is plurality with coordination: more than one skill is needed, and they must compose cleanly — consistent boundaries, no duplicate triggering, shared vocabulary, one place to ask what the whole set can do. Not for building a single standalone skill; use ordinary skill-creation for that. This is the architecture layer above individual skill authoring.
---

# Skill Suite Architect

Building several related skills one at a time, each written in isolation, produces skills that fight each other: two competing to trigger on the same request, none agreeing on what to call the same underlying concept, and no single place for anyone to ask "what can this whole thing do for me?" A suite is not "several skills" — it's several skills sharing one architecture. This skill designs and builds that architecture, not just the individual pieces.

## First, confirm a suite is actually the right shape

Multi-skill architecture only pays off when the domain genuinely decomposes into distinct threads of expertise that can be developed and triggered independently — the same principle that governs whether a multi-agent system is worth its overhead: architecture should follow the task's actual structure, not be imposed on it. A useful test: if the whole domain were crammed into one SKILL.md, would the description become an unreadable list of unrelated triggers, and would the body constantly branch — "if the request is about X, do this instead, but if it's about Y, do that instead"? That branching-on-role pattern is the real signal that several skills with a shared architecture are needed, not one skill with a lot of internal branches. If the roles are tightly interdependent and constantly need each other's full context mid-task rather than clean handoffs, a suite of separately-triggered skills may fight the task rather than help it — say so plainly rather than forcing a split that doesn't fit.

A recurring example worth keeping in mind throughout: an organization's leadership skill set — a skill for strategic and board-level framing, one for technical architecture and engineering tradeoffs, one for financial modeling, one for people and org-design questions. Each is a real, full-sized skill on its own. The suite-level work is making sure a request like "should we take on this new enterprise contract" gets routed to (or synthesized across) the right combination of them, rather than four skills each guessing they're the one that should answer.

## The five-stage process

### Stage 1 — Discover requirements

Before designing anything, establish:

- **Who uses this, and how?** One person switching between hats, or different people each using their own skill?
- **What are the candidate roles/functions?** List them and check each one actually needs distinct expertise and workflow — don't split roles that would really just be sections of the same skill (see the shape check above).
- **What does each candidate role own, and explicitly not own?** The boundary question matters more than the ownership question — for every role, ask what an adjacent person might assume it handles that actually belongs to a sibling instead.
- **What flows between roles?** Does one role's output ever need to become another role's input? Map these dependencies now, not after all the skills are written.
- **What already exists?** Check for skills or tools already in place that the new suite must integrate with rather than duplicate.
- **What counts as "done"?** What capability, if missing, would mean the suite isn't finished yet?

Don't move to planning until every candidate skill has one sentence stating what it's for and one stating what it explicitly is not for.

### Stage 2 — Create the plan

Turn the discovery into a concrete architecture with these named parts:

- **The roster.** Final list of member skills, each with a one-line mandate and an explicit scope boundary — what's in, what's out, and which sibling owns the "out" part.
- **The shared glossary.** Every term or concept referenced by more than one skill gets defined once, here, and used identically everywhere else. This is what stops one skill calling something a "risk register" while another calls the same thing an "issue log" — small inconsistencies like this are what make a suite feel like disconnected skills rather than one system.
- **The handoff contract.** For every cross-role dependency found in Stage 1, specify — for the producing skill — an objective, an expected output format, and for the consuming skill, what input it expects and from where. This four-part discipline (objective, output format, source/tool guidance, boundary) mirrors what makes delegation reliable in any multi-agent system: skip any one part and the receiving skill has to guess what "done" looks like, which is where handoffs quietly break.
- **Naming and layout convention.** The `name` field for every skill must stay lowercase letters, numbers, and hyphens only, under 64 characters, with no reserved words like "claude" or "anthropic" embedded in it. Anthropic's own guidance favors gerund form (`orchestrating-x`, `managing-y`) or a clear noun/action phrase — pick one pattern and use it for every member skill in the roster so the suite reads as one designed system rather than skills from different eras.
- **The Map artifact.** An index of the whole suite: every skill's name, one-line purpose, the kind of request it's for, and how it relates to its neighbors. Keep it one reference hop from the hub skill — never nested behind another reference file — so nothing about the suite's shape requires Claude to chase links to find out.
- **The hub skill's mandate.** Decide explicitly what the hub is responsible for: (a) telling a user in plain language what the whole suite can do and pointing them at the right member skill or combination, (b) helping Claude disambiguate when a request could plausibly belong to more than one member skill, and (c) nothing else — the hub must never duplicate a member skill's actual domain logic. A hub that starts answering finance-level questions itself instead of routing to the finance skill has stopped being a hub. Build in effort-scaling too: a simple, narrowly-scoped request should route to exactly one member skill, not fan out across the whole roster on the assumption that more skills firing means a better answer — that instinct is what makes multi-agent systems expensive and slow when the task never needed the breadth in the first place.

### Stage 3 — Create success metrics

Write these as concrete eval prompts before writing extensive documentation for any member skill — establishing what "correct" looks like first keeps the suite focused on real gaps rather than imagined ones. Suite-level quality needs checks that ordinary single-skill testing doesn't cover:

- **Trigger precision per skill** — does each member skill fire only on genuinely in-scope requests, the same bar as any standalone skill.
- **Non-overlap** — for requests near a boundary between two skills, does exactly one of them fire? Two skills firing on the same ambiguous request is the classic suite failure mode, and it's invisible if skills are only ever tested one at a time.
- **Coverage** — is there a realistic request type that no member skill claims? A gap in the roster is as much a failure as an overlap.
- **Handoff fidelity** — when a real task spans two roles, does the first skill's output actually work as input to the second without the user manually translating or reformatting it?
- **Hub accuracy** — does the hub correctly describe the suite's capabilities, route ambiguous or broad requests to the right member(s), and avoid over-fanning-out on simple ones?

Write ordinary per-skill prompts, a set of deliberately boundary-straddling prompts designed to test which skill should win, and at least one realistic multi-step scenario that should legitimately cross a handoff. As with any skill, plan to test across the models the suite will actually run on — what reads as sufficient guidance for a strong model may be too thin for a faster one.

### Stage 4 — Build the skills

With the architecture and metrics settled, write the actual files:

1. **Write the hub skill first.** It should contain, or point one hop away to, the Map, describe the suite's purpose and roster in plain terms, and carry its own description written the same way any skill's is: third person, specific about what it covers and when to reach for it, so it triggers reliably on requests like "what can you do" or "which skill handles this."
2. **Write each member skill using ordinary single-skill authoring practice** — progressive disclosure, a description that actually functions as a trigger mechanism rather than a summary, concise instructions that assume Claude is already capable and only add what it doesn't already know, and staying close to the usual length guidance (roughly 500 lines in the main body, with anything longer split into one-hop reference files). If a dedicated skill-authoring process is available in this environment, use it for the mechanics of writing and iterating on each individual skill — this process is specifically the layer above that, concerned with how the skills relate to each other, not how to author any one of them from scratch.
3. **Give every member skill a short "Boundaries" section near the top** stating plainly what it does not handle and which sibling to defer to instead. This is the single detail that most separates a skill written for a suite from a skill written in isolation — a solo skill rarely needs to say what it isn't for, but in a suite that sentence is often what prevents two skills from quietly fighting over the same request.
4. **Keep the Map current as you go.** Add each skill to it the moment it exists rather than reconstructing the Map from memory at the end — a Map that lags the real roster actively misleads rather than merely being incomplete.
5. **Use consistent terminology across every file**, drawn from the Stage 2 glossary — inconsistent terms for the same concept are harder for Claude to parse reliably than almost any other authoring mistake.

### Stage 5 — Test

Test at three levels; each catches a different failure mode, so don't skip to the last one:

1. **Per-skill.** Standard trigger-accuracy testing: does each member skill fire on its own realistic prompts, in isolation.
2. **Cross-boundary.** Load the whole suite's descriptions together, not one at a time, and run the boundary-straddling prompts from Stage 3. Confirm exactly one skill wins each time, and that it's the right one. This is the only level of testing that can actually catch overlap — per-skill testing in isolation structurally cannot, since there's nothing else present to compete with.
3. **End-to-end handoff.** Run at least one realistic scenario that legitimately spans two or more member skills in sequence, and confirm the handoff works without the user manually bridging the gap.

Where tests fail, fix the architecture, not just the wording — a repeated overlap usually means two skills' boundaries genuinely need redrawing, not just a cleverer description. Adjust the roster or boundary definitions in the plan, update the Map, then retest.

## What to hand back

- The Map (embedded in the hub skill for a small suite, or as its own short reference file, one hop away, for a larger one)
- The hub skill's SKILL.md
- Each member skill's SKILL.md, each carrying its own Boundaries section
- A short paragraph summarizing the final roster and how a request should be expected to route across it, so the architecture can be sanity-checked before real use

## Common pitfalls to check for before delivery

- **Reserved words or invalid characters in any `name` field** (no "claude" or "anthropic", lowercase/numbers/hyphens only, under 64 characters) and any `description` over 1,024 characters or written in first or second person rather than third.
- **Over-fragmentation** — roles split apart that never actually needed independent triggering, inflating the roster without adding real coverage.
- **A hub that quietly does domain work itself** instead of routing, which defeats the entire point of separating it from the member skills.
- **Handoffs specified only in prose "somewhere in the plan"** rather than as an explicit objective/format/boundary per dependency — this is the detail most likely to be skipped under time pressure and the one most likely to break in real use.
- **A Map that isn't updated** the moment a skill is added, renamed, or removed.