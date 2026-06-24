# Doc Template — Final Document Structure

Section order in the final `.docx`. **Write the OUTPUT in the user's chosen language** (see SKILL.md).
The labels below are in English for reference; translate them to the chosen output language.

---

## PART I — SYSTEM LEVEL

### 1. System Overview
- System name, brief purpose.
- Detected stack (backend, frontend, DB/ORM, auth).
- Stats: page count, module count, endpoint count.
- ⚠️ NEEDS CONFIRMATION: system name & business purpose if unclear from code/README.

### 2. Page Map / Feature List
Grouped per module. Table:

| Module | Page/Feature | Route | Role | File reference |
|--------|--------------|-------|------|----------------|

### 3. Main System Flow
- Brief end-to-end happy-path narrative.
- **Vertical swimlane** (embedded PNG). Lane = actor/role.
- ⚠️ This flow is almost always the result of user confirmation, not purely from code.

### 4. Sub-flows
For each key feature / small standalone flow (e.g. "Cancellation", "Password Reset", "Approval").
Each: brief narrative + vertical swimlane when it crosses actors.

### 5. User Story List (All Features)
See `references/user-stories.md`. Includes the **feature → story mapping table** for coverage audit.

### 6. Appendix
- **A. Open Gaps** — all unanswered `⚠️ NEEDS CONFIRMATION` items.
- **B. Decision Log** — decisions/assumptions confirmed by the user, with their answers.

---

## PART II — PER-PAGE LEVEL

One sub-section per page/feature. Template:

### [Page Name]
**Route:** `<method> /path`  ·  **Module:** ...  ·  **Role:** ...
**File reference:** `path/controller.ext:line`, `path/view.ext`

**Description**
2–4 sentences: what this page is for. (Technical function = fact; business purpose may be ⚠️.)

**Input**

| Field | Source | Type | Validation | Required |
|-------|--------|------|------------|----------|

(Source = form / route param / query / body. Validation taken from rules in code.)

**Output**
What is produced/rendered/returned. For APIs: response shape + status code.

**How It Works**
Logic steps in plain language (short bullets). Note side effects (DB, external API, queue, event, email).
Don't paste code — explain it.

**Main Flow**
User→system steps for the normal scenario. Vertical swimlane when it crosses actors.
⚠️ NEEDS CONFIRMATION when ordering/branching is uncertain from code.

**Alternative Flow / Edge Cases**
Failure branches, validation errors, special conditions. Some readable from conditionals; the rest ⚠️.

**Business Rules**
Numbered list. Separate:
- Readable from code (e.g. "max 3 items" from validation `max:3`) → include the reference.
- Needs confirmation → `⚠️ NEEDS CONFIRMATION`.

**User Story**
Story for this page/feature (format & AC → `references/user-stories.md`).

**Dependencies**
Other related pages/endpoints/services/models.

---

## Marking Conventions

- `⚠️ NEEDS CONFIRMATION: <question>` — inference not yet confirmed.
- `📎 ref: file.ext:line` — trace to the source code (inline is fine).
- After the user confirms, replace `⚠️` with the final content + record it in the Decision Log (Appendix B).
