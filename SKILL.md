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

try to keep the title under 80 charecters, is acceptable under 120, never more than 120

ALWAYS keep the body under 80 columns, wrap the text or present in a more
changelog style, with list, no long text

Break the lines in the right way, do not embed literal "\n" sequences in the
commit body. The body must contain real newline characters so `git log`/`gitk`
render it as multiple lines.

## Workflow

1) Detect current branch and any JIRA key.

- Command: `git rev-parse --abbrev-ref HEAD`
- Extract JIRA key using the regex and examples in `refs/jira-key-detection.md`.
- If the command returns `HEAD` (detached), skip branch-based detection and
  omit the JIRA prefix unless the user supplies a key explicitly.

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
- Type selection cheat sheet:
  - `fix`: Bug or defect correction.
  - `perf`: Performance improvement without behavior change.
  - `refactor`: Code change that neither fixes a bug nor adds a feature.
  - `feat`: New user-visible behavior or capability.
  - `docs`: Documentation-only change.
  - `chore`: Tooling, build, or maintenance tasks.
- Generated files and formatting-only changes:
  - Use `chore` for generated artifacts or build output updates.
  - Use `style` for formatting-only changes that do not affect behavior.
- Scope is optional; include it when a clear subsystem or package is targeted.
- Scope discovery guidance (pick one clear match, else omit):
  - Top-level directory or package name from `git diff --cached --stat`.
  - If a monorepo package changes dominate, use that package name.
  - If changes span many areas without a dominant one, omit scope.
- Scope formatting rules:
  - Lowercase, ASCII letters, digits, and dashes only.
  - No spaces or slashes.
- Mark breaking changes with `!` if applicable.

1) Build the commit title.

- Format: `type(scope)!: subject`
- If a JIRA key is present, prefix it:
  `JIRA-123 type(scope)!: subject`
- Subject is imperative, concise, and not capitalized as a sentence.
- Example pattern:
  `[JIRA-123] type(scope): description`

1) Build the commit body using Keep a Changelog sections.

- Use `refs/keep-a-changelog-v1.1.0.md` for section headers and bullet rules.
- Include only relevant sections; omit empty sections.
- Each bullet should be a user-meaningful change, not a file list.
- Start the body with a one-paragraph narrative description of the commit.
- Format body with one blank line between sections and between header and list.
- Wrap body lines to 80 characters (including bullet lines).
- Use `-` for bullets and keep one bullet per change.
- If a breaking change exists, include a `BREAKING CHANGE:` footer describing
  the impact and migration path.
- Example footer:
  `BREAKING CHANGE: rename "status" to "state"; update clients accordingly.`
- Example body:

  ```text
  Add a narrative summary of what changed and why, in one short paragraph.

  ### Added

  - Support workspace search with saved filters.

  ### Fixed

  - Avoid race when closing tabs on slow connections.
  ```

1) Run the commit.

- Prefer `git commit -F -` and pipe the body so newlines are preserved.
  Example:

  ```sh
  git commit -m "<title>" -F - <<'EOF'
  <body with real newlines, already wrapped to 80 columns>
  EOF
  ```

- If you do use `-m` for the body, ensure the shell passes real newlines
  (e.g., using a here-doc or `printf`) rather than a single line containing
  escaped `\n`.
- If the title fully captures a small, single change, and no narrative or
  changelog adds value, omit the body and use only the title.
- Example title-only commit:
  `docs: fix typo in readme`

## Notes

- Do not amend unless the user explicitly asks.
- Do not stage new changes if the user already staged specific files.
- If a JIRA key cannot be found, omit the prefix.
