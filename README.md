# AI-SKILLS-Library

A public library of [Claude Skills](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview) — reusable, self-contained instruction sets that extend what Claude can reliably do for a specific task or domain.

This repository is organized by **category**, with each category holding a set of related skills. It is actively maintained and will keep growing — see [Roadmap](#roadmap) for what's planned.

---

## What's a "Skill" here?

Every skill is a folder containing:
- **`SKILL.md`** — required. YAML frontmatter (`name`, `description`) + Markdown instructions Claude follows.
- **`scripts/`** *(optional)* — executable code the skill relies on.
- **`references/`** *(optional)* — extra docs loaded on demand for sub-workflows.
- **`assets/`** *(optional)* — templates, fonts, or other files used to produce output.

The `description` field is what allows Claude to automatically detect *when* to use a skill — it acts as a trigger, not just documentation.

---

## Categories

### Creator_Skills

Meta-skills for building and maintaining the skill library itself — used when the deliverable is *another skill*, not a regular task output.

| Skill | Purpose | Use it when… |
|---|---|---|
| **[skill-suite-architect](Creator_Skills/skill-suite-architect/SKILL.md)** | Designs and builds a *coordinated suite* of multiple skills for a complex domain that needs several specialized skills working together (e.g. a full leadership team's worth of advisory skills, or a set of department-specific skills that must not overlap). | You need more than one skill, and they must share vocabulary, avoid duplicate triggering, and hand off work to each other cleanly. |
| **[skill-translator](Creator_Skills/skill-translator/SKILL.md)** | Translates an existing skill (SKILL.md + any bundled files) from its original language into fluent, native-quality English — without breaking its triggering or behavior. | You've written or received a skill in another language and need an accurate, publish-ready English version. |

**Quick reference — trigger phrases:**
- *"I need a set of skills for our engineering, sales, and finance teams that work together"* → `skill-suite-architect`
- *"Translate this skill into English"* / *"Make my SKILL.md fluent English"* → `skill-translator`

---

## How to Use a Skill

1. Point Claude at the relevant `SKILL.md` (upload it, reference its path, or add it to your Claude environment's skills folder).
2. Describe your task naturally — Claude matches your request against the skill's `description` and applies it automatically.
3. No need to "invoke" a skill by name unless you want to force a specific one.

---

## Adding a New Skill

To keep the library consistent as it grows:

1. Place the skill under the right category folder (create a new category folder if none fits).
2. Keep `name` lowercase, hyphenated, under 64 characters, with no reserved words (`claude`, `anthropic`).
3. Write `description` in the third person, specific about *what* the skill does and *when* to reach for it.
4. Keep the main `SKILL.md` body to roughly 500 lines — move longer sub-workflows into `references/`.
5. Update this README's category table with the new skill's one-line purpose and trigger phrases.

---

## Roadmap

This library is under active development. Planned additions include:
- More Creator_Skills for skill testing/evaluation workflows
- Additional categories as new use cases are identified

*(This section will be updated as the library evolves — check back for changes.)*

---