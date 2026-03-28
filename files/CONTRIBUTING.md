# Contributing

## Branch Strategy

```
main
└── fix/issue-N-short-description
└── feat/issue-N-short-description
```

- `main` is the stable branch
- All work happens on a dedicated branch per issue
- Branch names reference the GitHub issue number

## Workflow

1. Open or find a GitHub Issue for the work
2. Create a branch from `main`:

```bash
git checkout main
git pull
git checkout -b fix/issue-3-backtick-handling
```

3. Make your changes
4. Test manually:

```bash
slug "your test input"
echo "piped input" | slug
```

5. Update `CHANGELOG.md` under `[Unreleased]`
6. Commit with a descriptive message:

```bash
git add .
git commit -m "fix: handle backticks in bash wrapper (#3)"
```

7. Push and open a PR:

```bash
git push -u origin fix/issue-3-backtick-handling
```

Then open a Pull Request on GitHub targeting `main`. The PR template will guide you.

## Commit Message Format

```
type: short description (#issue-number)
```

Types: `fix`, `feat`, `docs`, `chore`, `refactor`

## Testing

Always test with:

- plain ASCII input
- accented / multilingual input
- piped input via stdin
- input containing special characters
