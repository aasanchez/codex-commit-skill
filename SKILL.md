---
name: git-commit-conventional-changelog
description: Create or run an end-to-end Git commit using Conventional Commits title + Keep a Changelog body, auto-detect branch/JIRA key, and auto-stage only if nothing is staged. Use when the user asks to commit changes, generate conventional commits, include changelog-style body, or commit with a JIRA key from the branch name.
---

# Git Commit (Conventional + Changelog)

Follow this workflow to produce and run a full commit.

## Workflow

1) Detect current branch and any JIRA key.

- Command: `git rev-parse --abbrev-ref HEAD`
- Extract JIRA key using the regex and examples in `refs/jira-key-detection.md`.

2) Determine staging state and untracked handling.

- Check staging state with `git diff --cached --quiet`.
- Check working tree state with `git status --porcelain`.
- Treat tracked-file modifications as "unstaged changes" (`M`, `A`, `D`, `R`, `C` in the second column).
- Treat untracked files (`??`) separately:
  - If no staged changes exist and the user did not specify files, include untracked files when auto-staging with `git add -A`.
  - If staged changes exist, do not auto-stage anything else (including untracked).
- If there are no staged changes and no working tree changes (including `??`), stop and report nothing to commit.

3) Summarize changes to decide type/scope/body.

- Prefer `git diff --cached --stat` plus a quick scan of `git diff --cached`.
- Choose a Conventional Commit type from `refs/conventional-commits-v1.0.0.md`.
- Scope is optional; include it when a clear subsystem or package is targeted.
- Mark breaking changes with `!` if applicable.

4) Build the commit title.

- Format: `type(scope)!: subject`
- If a JIRA key is present, prefix it: `JIRA-123 type(scope)!: subject`
- Subject is imperative, concise, and not capitalized as a sentence.

5) Build the commit body using Keep a Changelog sections.

- Use `refs/keep-a-changelog-v1.1.0.md` for section headers and bullet rules.
- Include only relevant sections; omit empty sections.
- Each bullet should be a user-meaningful change, not a file list.

6) Run the commit.

- Use `git commit -m "<title>" -m "<body>"` (two -m blocks).
- If body is empty, use only the title.

## Notes

- Do not amend unless the user explicitly asks.
- Do not stage new changes if the user already staged specific files.
- If a JIRA key cannot be found, omit the prefix.
