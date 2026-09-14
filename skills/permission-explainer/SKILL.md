---
name: permission-explainer
description: Generates a plain-language permission explainer rule for AI coding tools. Use when the user wants to understand tool permission prompts and decide Once, Always, or Reject. Detects the harness including opencode and shows the exact global rules path to paste to.
license: MIT
version: 0.2.0
author: permission-explainer contributors
compatibility: bob, claude-code, codex, antigravity, cursor, opencode, gemini-cli
allowed-tools: Read, Glob
metadata:
  audience: non-technical
  language: en
  scope: global-rules-generator
---

# Permission Explainer

You generate a copy-paste global rule. You do not change any settings yourself.
Detection is read-only (`Read`, `Glob` only — no shell needed).

> Limit: this rule makes prompts readable. It cannot verify the agent's
> own description is true. Say this once when emitting the rule:
> "Explained does not mean safe — when in doubt, Reject."

## Steps

1. **Detect harness.** Check read-only signals in this priority order
   (first match wins for formatting; list ALL matches to the user):
   1. `.bob/` or `~/.bob/` -> IBM Bob
   2. `.opencode/`, `opencode.json`, `opencode.jsonc`, `~/.config/opencode/` -> opencode
   3. `~/.codex/` or `$CODEX_HOME` env set -> OpenAI Codex (global file is `$CODEX_HOME/AGENTS.md`, default `~/.codex/AGENTS.md`)
   4. `.claude/` or `~/.claude/` -> Claude Code
   5. `.cursor/rules/*.mdc` or `.cursor/` -> Cursor (ignore legacy `.cursorrules` for detection; it is deprecated)
   6. `~/.gemini/` or `.agents/rules/` -> Antigravity AND Gemini CLI share `~/.gemini/GEMINI.md` — always warn about the conflict (see `references.md`) and offer the `~/.gemini/AGENTS.md` split. Only treat as Antigravity-alone if the user confirms or an `antigravity` process/path is visible.
   - If no signal or 2+ signals: "I found signals for X and Y. Which tool(s)? Bob / Claude / Codex / Antigravity / Cursor / opencode / Gemini — pick any."

2. **Emit the rule block.** Use `examples.md` as source. Keep it verbatim.
   Harness formatting (only differences allowed):
   - Cursor local file — output a complete `.mdc` file, not bare Markdown:
     ```mdc
     ---
     description: Plain-language permission explainer
     alwaysApply: true
     ---
     # Permission explainer
     (... rule block verbatim ...)
     ```
     Cursor User Rules (UI path): plain Markdown, no frontmatter.
   - Antigravity: plain Markdown + the instruction "In Customizations, press `+ Global`, paste, and set the rule trigger to `Always On`." Never emit "Set this rule to Always On." as a rule sentence.
   - Claude Code `~/.claude/rules/` path: no `paths:` frontmatter (that is what makes it load every session).
   - Others: plain Markdown.

3. **Emit the paste target.** Look up `references.md`. Print:
   - Exact global path for THEIR tool and OS (Windows: `%USERPROFILE%` variants).
   - `mkdir -p` line if directory-based, or "append section" if file-based.
   - One verify step per tool:
     - Bob / opencode / Codex / Cursor / Antigravity: "Open a new session, trigger a permission prompt, you should see the permission explainer printed before you decide." (Codex: must restart — chain builds once per session.)
     - Claude Code: "Run `/memory`, confirm the file is listed, then trigger a permission prompt."
     - Gemini CLI: "Run `/memory show`, confirm the text is present, then trigger a permission prompt."

4. **Keep it plain.** English only. No jargon without translation. The permission explainer is always printed when permission is asked, before the user decides. Never skip the `Did (auto-approved)` past-tense variant. `expert: minimal` collapses to the 1-line form defined in `examples.md`; `expert: full` restores the full form. Tell the user both phrases exist when you emit the rule.
