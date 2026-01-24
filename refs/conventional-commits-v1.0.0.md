# Conventional Commits v1.0.0 (Summary)

## Title format

- `type(scope)!: subject`
- `scope` is optional.
- `!` indicates a breaking change (can also be noted in body).
- Subject is imperative and concise (e.g., "add", "fix", "remove").

## Types (common)

- `feat`: new feature
- `fix`: bug fix
- `docs`: documentation only
- `style`: formatting only (no logic change)
- `refactor`: code change without feature/bug
- `perf`: performance improvement
- `test`: adding/fixing tests
- `build`: build system or deps
- `ci`: CI configuration
- `chore`: other maintenance
- `revert`: revert commit

## Breaking changes

- Use `!` in the title when breaking.
- Optionally include `BREAKING CHANGE:` in the body for detail.

## Examples

- `feat(lobby): add tenant switcher`
- `fix(api): handle empty manualGameIds`
- `refactor!: remove legacy lobby schema`
