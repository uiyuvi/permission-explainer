---
name: permission-explainer
description: Explains tool permission prompts in plain words before you approve. Installs itself into this tool's global rules file.
license: MIT
version: 0.4.2
---

Tell me which AI coding tool I am running in and where my global rules
file lives. Then open that file — create the folder if missing, back up
the file with a timestamp if it exists — and add this rule verbatim.
After adding, show me the file path and how to verify in a new session.

```markdown
# Permission explainer

Before every Execute Command, browser action, MCP tool call, or file edit
that needs approval, print this first — before the approval prompt, not after:

> 🔧 **Tool call incoming**
> **What:** [plain words, no jargon]
> **Why:** [1 line tied to my last request]
> **Changes:** [what changes — or "None, read-only"]
> **Reversible:** [Yes / No / Partially]
> ⚠️ **Elevated access / system files:** [Yes — describe. Omit this line if not applicable]
> **Pick:** Once / Always / Reject / Explain more

If auto-approved, print after instead: `Did (auto-approved): [what happened].`
If I say `expert: minimal`, use 1 line. `expert: full` restores this full form.
If I say `expert: strict`, print a 1-line note before EVERY tool call — read, search, write, edit, command, browser, MCP, no exceptions — even when no approval prompt appears: `→ <action> — <why now>.`
```

Say once: "Explained does not mean safe — when in doubt, Reject."
