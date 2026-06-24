# User Stories — Format & Derivation from Code

Granularity: **Epic per module + Story per action (BMad style)**. **AC always included.**
Render stories in the user's chosen OUTPUT language; the format below is shown in English for reference.

---

## 1. Structure

```
EPIC <code>: <Module Name>
  Story <code>.1: <action title>
  Story <code>.2: ...
```

- **Epic = module** (e.g. "Reservation Management", "Authentication").
- **Story = one meaningful action** within the module (Create / Update / Delete / View / Approve / Export…).
  Split CRUD into separate stories when each action has its own meaning/rules.

---

## 2. Story Format

```
Story <code>: <short title>
As a <role/persona>,
I want <action>,
so that <benefit>.

Acceptance Criteria:
1. <criterion>
2. <criterion>
...

📎 ref: <handler file:line>
```

---

## 3. Derivation from Code (fact vs inference discipline)

| Part | Source | Status |
|------|--------|--------|
| **As a (role)** | auth/middleware/guard/policy gating the route | technical role = fact; business persona = ⚠️ |
| **I want (action)** | what the handler/page does | ✅ safest |
| **so that (benefit)** | — | ⚠️ NEEDS CONFIRMATION (the why is almost never in code) |
| **AC** | validation rules, conditionals, output, status code | code-derived = fact; business AC = ⚠️ |

Code-derived AC examples (usable directly, add a ref):
- From `max:3` → "The system rejects more than 3 items."
- From `required|email` → "Email is required and must be a valid format."
- From redirect/return → "On success, the user is redirected to <target>."

AC that must be confirmed (examples):
- What happens when stock runs out / payment fails (if not in code).
- Time limits, quotas, or business rules not encoded.

---

## 4. Feature → User Story Mapping Table (coverage audit)

Mandatory at the system level. Guarantees the "covers all features" claim is auditable.

| Module (Epic) | Page/Feature | Story | Status |
|---------------|--------------|-------|--------|
| Reservation | Create Reservation | RSV.1 | ✅ |
| Reservation | Cancel Reservation | RSV.2 | ⚠️ benefit needs confirmation |

- Every scanned page MUST appear at least once in this table.
- A page with no story → add a row with Status "❌ no story yet" so gaps are visible.

---

## 5. Notes

- Don't invent a benefit just to complete the format. Prefer `⚠️ NEEDS CONFIRMATION` over an assumption.
- A story may appear twice: brief in the page section (Part II) + full in the User Story List
  (Part I §5). Keep them consistent.
