---
name: codebase-doc-generator
description: >
  Thoroughly scan a codebase, then generate per-page feature documentation + user stories
  (epic per module, story per action in BMad style, with Acceptance Criteria) + main and sub system
  flows as vertical swimlanes, packaged into a single .docx file. Use this skill whenever the user asks
  to "document this codebase", "scan this project", "generate feature docs from code", "reverse engineer
  documentation", "generate user stories from a codebase", "brownfield documentation", or wants to
  understand an existing/legacy system from its source code. Also triggers on Indonesian phrases like
  "dokumentasiin codebase", "scan project ini", "bikin dokumentasi fitur dari kode". Runs in Claude Code
  (needs direct file access). Multi-stack: detect the stack first (Laravel, Vue, React, Next, Nuxt,
  Express, NestJS, Django, Flask, FastAPI, Rails, Spring, etc.) before scanning. Core discipline:
  SEPARATE facts-from-code vs inference, mark anything uncertain with "⚠️ NEEDS CONFIRMATION", and ASK
  about gaps (especially flows) before finalizing — never assume.
---

# Codebase Doc Generator

A skill to reverse-engineer documentation from source code: scan codebase → per-page feature docs →
user stories → system flows (vertical swimlanes) → a single `.docx` file.

**This skill runs the opposite direction of BMad.** BMad: idea → code. This skill: code → documentation.
Ideal for documenting legacy/existing systems, feature audits, or as a first step before BMad brownfield.

**Environment:** Claude Code (requires direct file access). If run without filesystem access, ask the
user to switch to Claude Code or upload the codebase.

---

## Output Language (IMPORTANT)

The generated document's language follows the **user's preference**, not a fixed default.
- If the user states a language, use it.
- If not stated, match the language the user is writing in, and confirm once before building.
- All section headings, tables, narratives, user stories, and swimlane labels in the OUTPUT use that
  chosen language consistently.

(These instruction files are in English; that is independent of the output language.)

---

## Core Principles (MANDATORY)

1. **Never assume.** Strictly separate:
   - **Facts from code** — what is actually readable in the source (routes, validation, return values, logic).
   - **Inference** — interpretation NOT certain from code. Mark it `⚠️ NEEDS CONFIRMATION`.
2. **Traceable.** Tag each fact with a `path/file.ext:line` reference where possible.
3. **Flows are not automatic.** UX ordering across pages, alternative branches, error handling, and the
   *why* behind conditionals are often NOT readable from code. These must be asked, not invented.
4. **Scan first, ask later.** Don't ask page-by-page. Scan thoroughly → draft → batch all gap questions
   at the end.

---

## Operating Flow (5 Phases)

### Phase 1 — DETECT
Identify the stack & project structure before scanning anything.
- Read markers: `composer.json`, `package.json`, `requirements.txt`, `pyproject.toml`, `Gemfile`,
  `go.mod`, `pom.xml`, `build.gradle`.
- Determine: language, backend framework, frontend framework, routing style (file-based vs declarative), DB/ORM.
- Per-stack detection heuristics → see `references/stack-detection.md`.
- **Output of this phase:** a stack summary + the list of locations to scan (routes, controllers, views,
  validators, auth). Show it to the user for confirmation before any heavy scanning.
- Also confirm the desired OUTPUT LANGUAGE here.
- If the stack is UNKNOWN → do not guess. Ask the user where route & handler definitions live.

### Phase 2 — SCAN
Map ALL pages/routes, then pull facts from code for each page:
- **Page/route list** — from the routing source matching the stack.
- **Input** — validation rules, route/query params, form fields in templates/components.
- **Output** — handler return value (view/JSON/redirect), status code, rendered data.
- **How it works** — summarize handler logic in plain language; note side effects (DB, external API,
  queue, event, email).
- **Auth/role** — middleware, guard, policy, decorator (used for the "As a" in user stories).
- **Dependencies** — other pages/endpoints/services/models invoked.
- Store each fact with a `file:line` reference.
- What is knowable vs not per stack → see `references/stack-detection.md`.

### Phase 3 — DRAFT + FLAG GAPS
Draft the documentation using the template in `references/doc-template.md`.
- Fill what can be filled from code facts.
- Whenever interpretation is needed (business intent, flow ordering, alternatives, *why*, role persona,
  user-story benefit, business-level AC) → write `⚠️ NEEDS CONFIRMATION: <specific question>`.
- Collect ALL `⚠️` markers into a single gap list.

### Phase 4 — GAP INTERVIEW (batched)
- Present all gap questions at once, grouped: **Flow**, **Business rules**, **Role/persona**,
  **User story (benefit & AC)**.
- Prioritize flow questions — those are least readable from code.
- Use closed/multiple-choice questions where possible for easy answering.
- Do NOT render swimlanes & build the docx before the main-flow gaps are answered. Unanswered minor gaps
  may stay in the document as "open" in the Appendix.

### Phase 5 — RENDER + BUILD
- Generate a vertical swimlane per flow → SVG → PNG. Visual conventions & rules → `references/swimlane-svg.md`.
- Build user stories (epic per module, story per action, AC always) → `references/user-stories.md`.
- Assemble everything into a single `.docx` with PNG swimlanes embedded → pipeline in `references/docx-build.md`.
- Include the feature → user-story mapping table (coverage audit) and an Appendix listing open gaps.

---

## Output Document Structure

**System level (opening sections):**
1. System Overview — stack, page/module counts, general picture
2. Page Map / Feature List (per module)
3. **Main System Flow** — end-to-end vertical swimlane
4. Sub-flows — small flows per key feature (each a swimlane when needed)
5. User Story List (all features) + feature → story mapping table
6. Appendix: list of unanswered "⚠️ NEEDS CONFIRMATION" items + decision log

**Per-page level (main body):**
Full template (description, input, output, how it works, flow, business rules, user story, dependencies)
in `references/doc-template.md`.

---

## Coverage Rules (anti-gap)

- Every scanned page MUST have a documentation entry.
- Every feature MUST have at least one user story in the mapping table.
- If a page is scanned but has no story / no flow yet → list it in the Appendix as an open item, never hide it.

---

## References

- `references/stack-detection.md` — stack-detection heuristics & per-stack locations of
  routes/handlers/validation, plus what is knowable vs needs-confirmation.
- `references/doc-template.md` — per-page & system-level documentation template (final docx structure).
- `references/user-stories.md` — BMad-style epic/story format, derivation rules from code, AC format,
  coverage mapping table.
- `references/swimlane-svg.md` — vertical swimlane conventions (aligned with `flowchart-maker`) for
  raster/docx output + SVG rules.
- `references/docx-build.md` — .docx build pipeline (Node `docx`) + SVG→PNG conversion + known MS Word
  compatibility fixes.
