# Doc Template — Final Document Structure

Section order in the final `.docx`. **Write the OUTPUT in the user's chosen language** (see SKILL.md).
The labels below are in English for reference; translate them to the chosen output language.

## Writing style (applies to the whole document)

This document must read as **complete, narrative documentation** — not a skeleton of bullets. Explain
each feature in flowing paragraphs that a stakeholder who never saw the code could follow. Use:
- **Paragraphs (prose)** for: Overview, Output, How It Works, Main Flow narrative, Alternative Flow narrative.
- **Tables** only for the structured Input field data.
- **Numbered lists** only for discrete Business Rules and Acceptance Criteria.
Avoid one-line bullet fragments as a substitute for explanation. A typical page entry should contain
several real paragraphs of text in addition to its tables and diagrams.

> Remember the Mandatory Confirmation Gate: by the time this document is generated, every
> `⚠️ NEEDS CONFIRMATION` item must already be resolved with the user. The final document should not
> contain unconfirmed guesses (only items the user explicitly chose to defer).

---

## PART I — SYSTEM LEVEL

### 1. System Overview
A few paragraphs describing the system as a whole: what it is, the problem it solves, who uses it, and
how the pieces fit together. Then state the detected stack (backend, frontend, DB/ORM, auth) and the
high-level statistics (page count, module count, endpoint count) within the prose.

### 2. Page Map / Feature List
A short introductory paragraph, then a table grouped per module:

| Module | Page/Feature | Route | Role | File reference |
|--------|--------------|-------|------|----------------|

### 3. Main System Flow
A paragraph describing the end-to-end happy path in plain language, followed by a **vertical swimlane**
(embedded PNG, lanes = actors/roles). The narrative and the diagram should agree.

### 4. Sub-flows
For each key feature / small standalone flow (e.g. "Cancellation", "Password Reset", "Approval"): a
descriptive paragraph plus a vertical swimlane when it crosses actors.

### 5. User Story List (All Features)
See `references/user-stories.md`. Includes the **feature → story mapping table** for coverage audit.

### 6. Appendix
- **A. Open Items** — only items the user explicitly chose to defer (normally empty).
- **B. Decision Log** — decisions/assumptions confirmed by the user during the gap interview, with answers.

---

## PART II — PER-PAGE LEVEL

One sub-section per page/feature, written as narrative documentation. Template:

### [Page Name]
**Route:** `<method> /path`  ·  **Module:** ...  ·  **Role:** ...
**File reference:** `path/controller.ext:line`, `path/view.ext`

**Overview**
A full paragraph (roughly 3–6 sentences) explaining what this page is, the role it plays in the system,
who interacts with it, and why it exists. Write it as prose, not bullets. Technical function is a fact;
business purpose, if it had to be confirmed, reflects the user's confirmed answer (no open guesses here).

**Input**
A short lead-in sentence, then the field table:

| Field | Source | Type | Validation | Required |
|-------|--------|------|------------|----------|

(Source = form / route param / query / body. Validation taken from rules in code.) After the table, add
a sentence or two of prose if any field needs explanation beyond the row.

**Output**
One or more paragraphs describing what the page produces or returns and what the user sees as a result.
For APIs, describe the response shape and status codes in prose (a small table is fine if it genuinely helps).

**How It Works**
The core of the entry: one to three paragraphs walking through the logic in plain language, as a
continuous explanation. Describe what happens when the page/handler runs, in what order, and what the
notable side effects are (DB writes, external API/service calls, queued jobs, events, emails/notifications,
file uploads). Name the services/models involved. Do not paste code and do not reduce this to a bullet
list — narrate it.

**Main Flow**
A paragraph describing the normal user→system scenario step by step in prose, followed by a vertical
swimlane when the flow crosses actors. (This flow reflects the user's confirmed answers.)

**Alternative Flow / Edge Cases**
A paragraph (or short paragraphs) covering failure branches, validation errors, and special conditions —
what happens and where the user ends up. Combine what is readable from conditionals in code with the
behaviour the user confirmed.

**Business Rules**
A numbered list; each rule is a complete sentence. Where a rule is readable from code, cite the reference
(e.g. "Maximum 3 items per request — `app/Http/Requests/Foo.php:21`").

**User Story**
The story/stories for this page/feature (format & AC → `references/user-stories.md`).

**Dependencies**
A sentence or short paragraph naming the other pages/endpoints/services/models this feature relies on and
how they relate.

---

## Marking Conventions (internal working draft ONLY)

These markers exist purely to drive the gap interview. They are **forbidden in the final document** —
strip/replace every one of them before exporting (the only exception is an item the user explicitly
deferred, which is rewritten as clean prose in Appendix A, not as a raw marker).
- `⚠️ NEEDS CONFIRMATION: <question>` — inference not yet confirmed; must be asked, then removed.
- `📎 ref: file.ext:line` — trace to the source code (this one MAY stay in the final doc where useful).
- After the user confirms, replace the `⚠️` line with the final prose + record it in the Decision Log (Appendix B).
- Before export, search the whole document for `⚠️` / `NEEDS CONFIRMATION` / `❓` / `TBD` / `TODO` /
  `<...>`. If anything matches, the document is NOT ready — go back to the interview.
