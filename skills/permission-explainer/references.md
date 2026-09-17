# Global rules paths

Where to paste the generated rule. Global = every workspace and session.
Verified 2026-09-14 against each tool's official docs (links in Source).

| Tool | Global rules path | How to add | Source |
|---|---|---|---|
| Claude Code | `~/.claude/rules/permission-explainer.md` (modular; no `paths:` frontmatter = loads every session) — or append to `~/.claude/CLAUDE.md` (primary global file) | `mkdir -p ~/.claude/rules`, then create the file. Verify with `/memory` | [Memory](https://code.claude.com/docs/en/memory) |
| OpenAI Codex | `~/.codex/AGENTS.md` (`$CODEX_HOME/AGENTS.md` if `CODEX_HOME` is set) | `mkdir -p ~/.codex`, append section. Note: `AGENTS.override.md` at the same level wins if present; keep global ≤5 KiB (32 KiB chain cap); restart session to reload | [AGENTS.md guide](https://developers.openai.com/codex/guides/agents-md) |
| Cursor | User Rules (`Customize > Rules`, synced to account) **or** local file `~/.cursor/rules/permission-explainer.mdc` | Must be `.mdc`, not `.md` — plain `.md` in `.cursor/rules/` is ignored. Frontmatter must contain `alwaysApply: true`. Do not use legacy `.cursorrules` for new installs | [Rules](https://cursor.com/docs/rules) |
| Antigravity | `~/.gemini/GEMINI.md` via `+ Global` in Customizations | Paste block, set rule trigger to `Always On` (not a magic string — it's the UI trigger type). Cross-tool alternative since v1.20.3: `~/.gemini/AGENTS.md`; `GEMINI.md` wins on conflict | [Rules](https://antigravity.google/docs/ide/rules/) |
| opencode | `~/.config/opencode/AGENTS.md` (instructions) | `mkdir -p ~/.config/opencode`, append section. Note: `opencode.json`/`opencode.jsonc` is config (model, permissions); `AGENTS.md` is the instruction text. `~/.claude/CLAUDE.md` acts as fallback only when the opencode global file is absent | [Rules](https://opencode.ai/docs/rules/) |
| Gemini CLI | `~/.gemini/GEMINI.md` | `mkdir -p ~/.gemini`, append section. Verify with `/memory show` | [GEMINI.md](https://google-gemini.github.io/gemini-cli/docs/cli/gemini-md.html) |
| IBM Bob | `~/.bob/rules/permission-explainer.md` | `mkdir -p ~/.bob/rules`, then create the file. Workspace equivalent: `.bob/rules/` | [Custom rules](https://bob.ibm.com/docs/ide/configuration/rules) |

## Known conflict: Antigravity + Gemini CLI share one file

Both default to `~/.gemini/GEMINI.md`. Instructions meant for one leak
into the other. Workaround: keep shared text in `~/.gemini/AGENTS.md`
(Antigravity ≥v1.20.3 reads it; Gemini CLI reads it if you add it to
`context.fileName` in `settings.json`), keep `GEMINI.md` minimal with a
comment header separating per-tool sections.

## Windows

Replace `~` with `%USERPROFILE%`:

- Claude Code: `%USERPROFILE%\.claude\rules\permission-explainer.md` or `%USERPROFILE%\.claude\CLAUDE.md`
- Codex: `%USERPROFILE%\.codex\AGENTS.md`
- Cursor: `%USERPROFILE%\.cursor\rules\permission-explainer.mdc`
- Antigravity / Gemini CLI: `%USERPROFILE%\.gemini\GEMINI.md`
- opencode: `%USERPROFILE%\.config\opencode\AGENTS.md`
- Bob: `%USERPROFILE%\.bob\rules\permission-explainer.md`
