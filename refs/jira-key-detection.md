# JIRA key detection

## Regex

- `\b([A-Z][A-Z0-9]+-\d+)\b`

## Branch examples (match)

- `feature/ABC-123-add-tenant-switcher` -> `ABC-123`
- `bugfix/PLAT-9` -> `PLAT-9`
- `ABC-456` -> `ABC-456`

## Notes

- Prefer the first match in the branch name.
- Do not fabricate a key if none found.
