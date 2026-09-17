# AGENTS.md — contributor rules for this repo

This repo is purely the `permission-explainer` skill. Nothing else.

- English only. Plain words, no jargon without translation.
- Skill source of truth: `skills/permission-explainer/SKILL.md`.
  Keep `examples.md` and the installed rule block byte-identical.
- Frontmatter follows https://agentskills.io/specification
  (`name`, `description`, `license`, `metadata` only — no top-level
  `version`; version lives under `metadata:` and in git tags).
- Screenshots in `assets/` must be real runs, cropped tight, no secrets.
  Synthetic SVGs are labeled as such in `assets/README.md`.
- Commit style: amend into one commit per release; tag `vX.Y.Z`
  matching `metadata.version`. Bump `metadata.version` and the
  `.claude-plugin/marketplace.json` version together on release.
- Never commit secrets, tokens, or private workspace files.
