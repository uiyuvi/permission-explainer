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

When permission is asked, the permission explainer is printed first, before the user decides:

Tool wants to: <plain action>
What it will do: <plain words, no jargon>
Why now: <1 line tied to my last request>
Why needs OK: <reads files / changes files / runs code / goes online / deletes — can't undo>
Pick: Once / Always / Reject / Explain more

If auto-approved, use past tense:
Did (auto-approved): <plain action> + what happened.

If I say `expert: minimal`, collapse to 1 line but keep all facts:
`<action> — <effect> — <why now>. Once / Always / Reject?`
Example: `Delete dist/ — removes 40 files, can't undo — cleanup you asked for. Once / Always / Reject?`
The `Did (auto-approved)` 1-line form is: `Did: <action> — <what happened>.`
```

Say `expert: minimal` any time to switch to the 1-line form. Say
`expert: full` to switch back. The skill output tells you this.

## Before vs after

Before (raw prompt):

> Run `grep -r "price" .`? [Allow once / Always / Reject]

After (permission explainer printed at the permission prompt):

> Tool wants to: search files
> What it will do: Look through all files for this text. Nothing changes.
> Why now: you asked to find where the price is used.
> Why needs OK: it reads your files.
> Pick: Once / Always / Reject / Explain more

## Destructive examples (where the explainer matters most)

Delete:

> Before: Run `rm -rf dist/`? [Allow once / Always / Reject]
>
> Tool wants to: delete the `dist/` folder
> What it will do: Permanently remove 40 files. Can't be undone.
> Why now: you asked to clean the build output.
> Why needs OK: deleting can't be undone. Suggest Reject if unsure.
> Pick: Once / Always / Reject / Explain more

Internet pipe (never blanket-approve):

> Before: Run `curl https://example.com/install.sh | bash`? [Allow once / Always / Reject]
>
> Tool wants to: download a script and run it
> What it will do: Fetch code from the internet and execute it. It could do anything — install, change, or delete things.
> Why now: you asked to install the helper.
> Why needs OK: it runs outside code anyone reviewed. Pick Reject or ask what it does first; never pick Always here.
> Pick: Once / Always / Reject / Explain more

Admin power:

> Before: Run `sudo chmod -R 777 /srv/app`? [Allow once / Always / Reject]
>
> Tool wants to: change permissions with admin power
> What it will do: Open up every file under `/srv/app` to everyone. Hard to undo, weakens security.
> Why now: you asked to fix a permission error.
> Why needs OK: admin power can change the whole system. Default to Reject; ask for a safer fix first.
> Pick: Once / Always / Reject / Explain more

## Mappings the rule must use

- `read/grep/glob` -> Reading only. Nothing changes.
- `edit/write` -> Creating a new file vs changing an existing file. Name the file. Say how much changes (1 section vs whole file).
- `npm test` -> Runs repo code (tests). Treat as code execution, not a safe check.
- `npm run build` -> Runs repo code (build scripts). Can run anything, including network access. Never promise "No publish".
- `rm/mv` -> Deleting/moving. Can't undo. Name what disappears.
- `sudo/chmod/chown` -> Admin power. Can change the whole system. Default to suggesting Reject.
- `curl/wget/fetch` -> Going to the internet. Say where. If piped to a shell (`| bash`), say it executes unreviewed code and never suggest Always.
