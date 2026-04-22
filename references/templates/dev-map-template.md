# Dev Map

Last Updated: <YYYY-MM-DD HH:MM TZ>

## Start Here

- Read `progress.txt` first.
- Then read the active exec-plan referenced there.
- Use this file to find the relevant code/docs.

## Project Map

| Area | Path | Responsibility |
|---|---|---|
| <area> | `<path>` | <responsibility> |

## Common Change Paths

### Add or change a feature

1. Read `<path/doc>`.
2. Update `<path>`.
3. Add or update tests.
4. Run `scripts/verify.<sh|ps1>`.

### Fix a bug

1. Reproduce or identify the failing path.
2. Read the smallest related module.
3. Add regression coverage when possible.
4. Run `scripts/verify.<sh|ps1>`.

## Protected Areas

- `<path>`: <why it needs care or approval>

## Existing Patterns

- <pattern to follow>

## Harness References

- `docs/harness.md`
- `.agents/skills/<skill-name>/` when applicable
- `.codex/rules/*.rules` when applicable
