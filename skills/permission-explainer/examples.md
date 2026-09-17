# Examples

> Safety note: this rule makes permission prompts readable. It does not
> verify that the agent's description is true. An agent can describe a
> destructive action in harmless words. Explained does not mean safe —
> when in doubt, pick Reject and ask for more detail. Pair this rule
> with least-privilege settings (auto-deny `sudo`/`rm -rf`) where your
> tool supports them.

## The rule block (what the skill outputs for copy-paste)

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

The `expert: minimal` 1-line form keeps all facts:
`<action> — <effect> — <why now>. Once / Always / Reject?`
Example: `Delete dist/ — removes 40 files, can't undo — cleanup you asked for. Once / Always / Reject?`
The `Did (auto-approved)` 1-line form is: `Did: <action> — <what happened>.`

Say `expert: minimal` any time to switch to the 1-line form. Say
`expert: full` to switch back. The skill output tells you this.

## Before vs after

Before (raw prompt):

> Run `grep -r "price" .`? [Allow once / Always / Reject]

After (permission explainer printed at the permission prompt):

> 🔧 **Tool call incoming**
>
> **What:** Look through all files for this text. Nothing changes.
> **Why:** you asked to find where the price is used.
> **Changes:** None. Only reading.
> **Reversible:** Yes — nothing changed.
> **Pick:** Once / Always / Reject / Explain more

## Destructive examples (where the explainer matters most)

Delete:

> Before: Run `rm -rf dist/`? [Allow once / Always / Reject]
>
> 🔧 **Tool call incoming**
>
> **What:** delete the `dist/` folder — permanently remove 40 files.
> **Why:** you asked to clean the build output.
> **Changes:** 40 files gone. Can't be undone.
> **Reversible:** No. Suggest Reject if unsure.
> **Pick:** Once / Always / Reject / Explain more

Internet pipe (never blanket-approve):

> Before: Run `curl https://example.com/install.sh | bash`? [Allow once / Always / Reject]
>
> 🔧 **Tool call incoming**
>
> **What:** download a script and run it.
> **Why:** you asked to install the helper.
> **Changes:** Fetches code from the internet and executes it. It could do anything — install, change, or delete things.
> **Reversible:** Partially at best. Pick Reject or ask what it does first; never pick Always here.
> **Pick:** Once / Always / Reject / Explain more

Admin power:

> Before: Run `sudo chmod -R 777 /srv/app`? [Allow once / Always / Reject]
>
> 🔧 **Tool call incoming**
>
> **What:** change permissions on every file under `/srv/app`.
> **Why:** you asked to fix a permission error.
> **Changes:** Every file opened up to everyone. Hard to undo, weakens security.
> **Reversible:** Partially. Default to Reject; ask for a safer fix first.
> ⚠️ **Elevated access / system files:** Yes — admin power can change the whole system.
> **Pick:** Once / Always / Reject / Explain more

## Mappings the rule must use

- `read/grep/glob` -> Reading only. Nothing changes.
- `edit/write` -> Creating a new file vs changing an existing file. Name the file. Say how much changes (1 section vs whole file).
- `npm test` -> Runs repo code (tests). Treat as code execution, not a safe check.
- `npm run build` -> Runs repo code (build scripts). Can run anything, including network access. Never promise "No publish".
- `rm/mv` -> Deleting/moving. Can't undo. Name what disappears.
- `sudo/chmod/chown` -> Admin power. Can change the whole system. Default to suggesting Reject.
- `curl/wget/fetch` -> Going to the internet. Say where. If piped to a shell (`| bash`), say it executes unreviewed code and never suggest Always.
