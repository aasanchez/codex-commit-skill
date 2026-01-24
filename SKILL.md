---
name: git-commit-conventional-changelog
description: >
  Create or run an end-to-end Git commit using Conventional Commits title +
  Keep a Changelog body, auto-detect branch/JIRA key, and auto-stage only if
  nothing is staged. Use when the user asks to commit changes, generate
  conventional commits, include changelog-style body, or commit with a JIRA key
  from the branch name.
---

# Git Commit (Conventional + Changelog)

This skill guides a consistent, end-to-end commit that combines a Conventional
Commits title with a Keep a Changelog body.

Follow this workflow to produce and run a full commit.

## Workflow

1) Detect current branch and any JIRA key.

- Command: `git rev-parse --abbrev-ref HEAD`
- Extract JIRA key using the regex and examples in `refs/jira-key-detection.md`.

1) Determine staging state and untracked handling.

- Check staging state with `git diff --cached --quiet`.
- Check working tree state with `git status --porcelain`.
- Treat tracked-file modifications as "unstaged changes" (`M`, `A`, `D`, `R`,
  `C` in the second column).
- Treat untracked files (`??`) separately:
  - If no staged changes exist and the user did not specify files, include
    untracked files when auto-staging with `git add .`.
  - If staged changes exist, do not auto-stage anything else (including
    untracked).
- If there are no staged changes and no working tree changes (including `??`),
  stop and report nothing to commit.
- Example `git status --porcelain` lines:
  - `M src/app.ts` (tracked file modified, unstaged)
  - `M  src/app.ts` (tracked file modified, staged)
  - `?? new-file.md` (untracked)

1) Summarize changes to decide type/scope/body.

- Prefer `git diff --cached --stat` plus a quick scan of
  `git diff --cached`.
- Choose a Conventional Commit type from `refs/conventional-commits-v1.0.0.md`.
- Scope is optional; include it when a clear subsystem or package is targeted.
- Scope discovery guidance (pick one clear match, else omit):
  - Top-level directory or package name from `git diff --cached --stat`.
  - If a monorepo package changes dominate, use that package name.
  - If changes span many areas without a dominant one, omit scope.
- Mark breaking changes with `!` if applicable.

1) Build the commit title.

- Format: `type(scope)!: subject`
- If a JIRA key is present, prefix it:
  `JIRA-123 type(scope)!: subject`
- Subject is imperative, concise, and not capitalized as a sentence.

1) Build the commit body using Keep a Changelog sections.

- Use `refs/keep-a-changelog-v1.1.0.md` for section headers and bullet rules.
- Include only relevant sections; omit empty sections.
- Each bullet should be a user-meaningful change, not a file list.

1) Run the commit.

- Use `git commit -m "<title>" -m "<body>"` (two -m blocks).
- If body is empty, use only the title.

## Notes

- Do not amend unless the user explicitly asks.
- Do not stage new changes if the user already staged specific files.
- If a JIRA key cannot be found, omit the prefix.
