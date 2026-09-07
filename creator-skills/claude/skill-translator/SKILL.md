---
name: skill-translator
description: Translates an existing Claude Skill (a SKILL.md file plus any bundled scripts/references/assets) from its original authoring language into fluent, native-quality English while preserving its exact behavior. Use this whenever the user wants to convert, translate, localize, or port a skill written in a non-English language into an English version — including requests phrased as "translate this skill into English," "make my skill's SKILL.md in English," "port this skill to English so I can publish it," or "I wrote this skill in [language], can you give me an English SKILL.md." Also use when the user is building a skills repository intended for a mixed or international audience and asks for an English copy of a non-English skill, or when comparing/auditing a skill's frontmatter and body against its non-English source for accuracy. Do not use this for translating ordinary documents, chat messages, or general text — it is specifically for the SKILL.md format (YAML frontmatter + Markdown body + optional scripts/references/assets folders).
---

# Skill Translator

A skill for converting another skill's authoring language into English — accurately, fluently, and without breaking anything that makes the original skill work.

## Why this needs its own process

Translating a skill is not the same job as translating a blog post. A SKILL.md is a *program written in prose* — Claude reads it and follows it literally. Two things make this task easy to get subtly wrong if you just run a generic translation pass:

1. **The description field is a routing mechanism, not a summary.** It determines whether Claude ever notices the skill exists. A grammatically perfect but flat translation can quietly kill triggering even though every sentence is "correct."
2. **Not all non-English text in a skill is meta-instruction.** Some of it is *content the skill is designed to produce or operate on* — e.g. a skill whose entire job is to grade essays in a specific language, or a template that must stay in the original language because that's the language the end output is supposed to be in. Translating that text would silently change what the skill does, which is a functional bug, not a translation nicety.

Your job is to produce an English version that a native English-speaking Claude would trigger correctly and follow exactly as intended — never a version that merely "looks like" a translation.

## Step 0: Gather the full skill, not just SKILL.md

Before translating anything, locate the complete skill folder. A skill can be just one file, or it can be:

```
skill-name/
├── SKILL.md
├── scripts/       (executable code)
├── references/    (extra docs loaded on demand)
└── assets/        (templates, fonts, icons used in output)
```

Read all of it before starting. If `SKILL.md` points to a `references/` file for a sub-workflow, you need to read and translate that file too — an English SKILL.md that still tells Claude to "go read reference.md for the naming conventions" but reference.md was never translated is a broken skill, not a finished one. If you only have `SKILL.md` and can't find the rest of the folder, ask the user for the remaining files rather than guessing at their contents.

## Step 1: Classify every piece of non-English text before touching it

Before translating line-by-line, sort the skill's content into two buckets. This single step prevents the most common failure mode.

**Bucket A — Instructional language (translate this).**
Anything written *to Claude*, telling it what to do, when to do it, or how to structure its work: the description, workflow steps, writing-pattern guidance, formatting rules, explanations of *why* a step matters, section headers, checklist items.

**Bucket B — Functional content (do not translate, unless the user explicitly asks you to).**
Anything that is *part of the skill's actual output or required behavior*, where the language itself is load-bearing:
- An instruction that the skill's *output* must be in a specific language (e.g. "always answer the user in [language X]") — this line describes required behavior, and that behavior is "produce language X," so it should stay accurate to that requirement even as you translate the sentence *around* it into English. Translate the instruction, never the requirement it's describing.
- Example inputs/outputs that demonstrate the skill working in a specific language, where the whole point of the example is to show real, idiomatic native output (e.g. a sample question in the original language inside a quiz-generation skill).
- Proper nouns, named exam formats, institution names, or region-specific terms that don't have a natural English equivalent — keep the original term and add a short English gloss in parentheses on first use rather than inventing a translation that doesn't exist in English usage.
- Any code, variable names, file paths, or CLI commands inside script blocks — code is not prose and must never be translated. Comments inside code *can* be translated since they're written for a human reader, but be conservative: if a comment references a specific non-English business term with no clean English equivalent, leave it or gloss it.

When in doubt about which bucket something belongs to, ask: *"If I translate this, does the skill still do the same job for the same kind of user?"* If yes, it's Bucket A. If translating it would change what gets produced or who it works for, it's Bucket B.

## Step 2: Build a glossary before translating the body

Skim the whole skill first and list the domain-specific terms that repeat (subject names, exam sections, tool names, internal jargon the author invented). Decide one consistent English rendering for each *before* you start translating paragraph by paragraph, and keep a running glossary as you work. This is what keeps a 400-line skill from drifting — using "practice test" in one section and "mock exam" in another for the same concept reads as sloppy and can confuse Claude about whether they're the same thing.

If a term has no clean single-word English equivalent, prefer a short descriptive phrase over a stiff literal translation, and stay consistent with whichever choice you make.

## Step 3: Translate the frontmatter first — this is the highest-leverage part

```yaml
---
name: <identifier>
description: <the routing text>
---
```

**`name`:** This is a machine identifier and should usually stay lowercase, hyphenated, ASCII. If the original name is already in this form, keep it unchanged so the skill's identity doesn't break for anything already referencing it. If the original name is written in the source language's script, choose a short, descriptive English kebab-case name that reflects what the skill does (not a transliteration) — note the rename clearly for the user, since anything that referenced the skill by its old name will need updating.

**`description`:** Never translate this sentence-by-sentence and call it done. Rebuild it in English with the same two jobs the original was doing:
1. State plainly what the skill does.
2. Give Claude enough concrete triggering signal to reach for it — the specific tasks, file types, and phrasings a real user would use.

Per the general skill-authoring guidance, descriptions should lean slightly assertive about when to trigger, rather than a bare one-line summary — list out the concrete situations, not just the abstract category. Crucially, **write the trigger phrases in the language the *translated* skill's users will actually type in** — usually English — rather than literally translating the original's example phrases word-for-word. A source-language description that lists native idioms for "quiz me" should become natural English trigger phrases like "quiz me on this," "test my understanding," or "make flashcards from this file" — equivalent trigger coverage, native phrasing, not a gloss of the original idiom. If the skill may still sometimes be invoked in its original language by a bilingual user, it's fine to keep one or two of the original-language example phrases alongside the English ones, but the bulk of the trigger examples should be idiomatic English.

## Step 4: Translate the body section by section

Work through the Markdown body preserving structure exactly — same headers, same nesting, same ordering, same tables, same numbered steps. Do not reorganize or "improve" the original skill's logic; your job is translation, not a redesign. Within that constraint:

- Write in natural, direct English — the same imperative, explain-the-why style the skill-authoring guidance itself recommends (e.g. "Read the file before editing it, since editing without reading risks overwriting content you haven't seen" rather than a stiff transliteration of the original's sentence structure).
- Preserve emphasis patterns (ALWAYS/NEVER/MUST) exactly where the original uses them for genuinely hard constraints — don't soften a real hard rule into a suggestion, and don't invent new hard rules that weren't there.
- Leave code blocks, file paths, and CLI commands completely untouched.
- Translate comments within code blocks only when they're explanatory prose for a human reader, and only when a clean English equivalent exists (see Step 1, Bucket B).
- Keep tables aligned and keep the same column structure — translate cell contents, not column count or ordering.
- If a Markdown link points to a reference file, translate the link *text* but keep the file path pointing at the correct (now-also-translated) file.

## Step 5: Handle bundled resources

- **`references/*.md`** — translate the same way as the main body, applying the same Bucket A/B split and the same glossary.
- **`scripts/*`** — leave the code logic untouched. Translate only human-facing strings the script prints or writes into output documents if the user wants the tool's own output to be in English too — ask if this isn't obvious from context, since some scripts print progress messages that only the developer sees and don't need translating at all.
- **`assets/*`** — templates (docx/pptx templates, letterheads) may contain placeholder text baked into the file. Only touch this if the user confirms the *output documents* should be in English; otherwise leave assets alone, since they're often reused across both language versions of a skill.

## Step 6: Self-review before handing it back

Before presenting the result, check:

- [ ] Frontmatter is valid YAML, `name` is a clean identifier, `description` reads as fluent native English and gives concrete triggering coverage, not a literal gloss of the original.
- [ ] Every Bucket B item (functional non-English content) is still present and unchanged, and you can point to *why* each one was left alone if asked.
- [ ] No leftover source-language text remains in Bucket A material — do a scan for the original script/language and confirm every remaining instance is a deliberate Bucket B decision, not a missed sentence.
- [ ] All code blocks are byte-for-byte identical to the original except for deliberately translated comments.
- [ ] Markdown structure (headers, nesting, tables, numbered lists) matches the original's shape.
- [ ] The skill is still under roughly 500 lines, or you've flagged to the user that it's grown past that and suggested moving a section into `references/`.
- [ ] If `name` changed, you've told the user clearly what the old and new identifiers are.

## Step 7: Deliver both versions together

Never overwrite the original skill file. Produce the English version as a sibling — either a new file (e.g. `SKILL.md` translated content saved as `SKILL.en.md`, or in its own `skill-name-en/` folder if you're also translating bundled resources) so the user keeps the original working skill alongside the new English one, and can diff them side by side if something looks off. When you hand the result back, briefly flag anything you filed under Bucket B (left untranslated on purpose) so the user can double check your judgment call rather than discovering it silently later.