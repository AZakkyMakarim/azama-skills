# azama-skills

System Analyst tooling in the **Agent Skills (SKILL.md)** format — an open standard supported by 30+
tools (Claude Code, Codex CLI, Cursor, Gemini CLI, VS Code Copilot, and more).

This repo is **dual-purpose**:
- **Claude Code users** → clean install via the marketplace (`/plugin install`, with auto-update).
- **Other tools** → just grab the skill folder under `skills/` and drop it into your tool's skills directory.

Single source (`skills/`), no duplication.

## Available skills

### `codebase-doc-generator`
Thoroughly scan a codebase → a single `.docx` containing: per-page feature documentation (description,
input, output, how it works, main & alternative flows, business rules, dependencies), **user stories**
(epic per module, story per action in BMad style, AC always included), the **main system flow + sub-flows**
as vertical swimlanes (embedded images), plus a feature → story mapping table and an open-gaps appendix.

Multi-stack (Laravel, Vue, React, Next, Nuxt, Express, NestJS, Django, Flask, FastAPI, Rails, Spring,
etc.). Anti-assumption discipline: separate facts-from-code vs inference, mark `⚠️ NEEDS CONFIRMATION`,
and ask about gaps (especially flows) before finalizing.

> The generated document is written in **the user's chosen language**, not a fixed default.
> Requires a tool with file access + code execution (agent mode).

---

## Install

### A. Claude Code (via marketplace)
```bash
/plugin marketplace add azama/azama-skills
/plugin install codebase-doc-generator@azama-skills
```
> Replace `azama/azama-skills` with your actual GitHub `owner/repo`.
> Update: `/plugin marketplace update azama-skills`

### B. Other tools (grab the skill folder)
The raw skill lives in `skills/codebase-doc-generator/`. Clone the repo and copy it into your tool's
skills directory:

```bash
git clone https://github.com/AZakkyMakarim/azama-skills.git

# Codex CLI
cp -r azama-skills/skills/codebase-doc-generator ~/.codex/skills/

# Claude Code (manual, without the marketplace)
cp -r azama-skills/skills/codebase-doc-generator ~/.claude/skills/

# Cursor / VS Code Copilot / Antigravity / others:
# check that tool's skills directory in its docs, then copy the same folder there
```

Or use a cross-tool registry (e.g. Vercel `npx skills`, Agensi, SkillUse) that automatically places the
skill into each tool's directory.

---

## Security note
This skill can execute code/scripts (scanning files, SVG→PNG conversion, building the `.docx`). Like
installing software — **only use skills from sources you trust.** This repo is public and auditable before install.

## License
MIT.
