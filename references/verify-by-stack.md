# Verification Script Generation by Tech Stack

Use this reference when generating `scripts/verify.*` to produce a script with actual meaningful checks, not just a framework placeholder.

## Composition Model

Every verification script is built from three layers:

1. **Universal gates** — apply to every project regardless of stack
2. **Stack gates** — derived from the detected language, framework, and tooling
3. **Project gates** — derived from business rules, forbidden patterns, and conventions discovered during inspection

Inspect the repo first. Derive stack gates from manifests, CI files, and build scripts. Derive project gates from existing rules, comments, doc conventions, and any `.codex/rules/*.rules` or `AGENTS.md` prohibitions already present.

Record the final gate list in `docs/harness.md` under the Verification section.

---

## Universal Gates

Include these in every script regardless of stack:

- **Build passes** — compile or bundle without errors
- **Tests pass** — all test suites exit 0
- **Test count does not regress** — fail if total test count drops vs. the last known baseline; prevents silent test deletion
- **No secrets or credentials in source** — scan for API keys, tokens, passwords hardcoded in non-test files
- **No merge conflict markers** — scan for `<<<<<<<`, `=======`, `>>>>>>>` in source files
- **Harness doc freshness check** — warn if `docs/dev-map.md`, `docs/DESIGN.md`, or `AGENTS.md` have not been touched in a configurable number of days relative to the last source change

---

## Stack Gates

### Node.js / TypeScript / Frontend

Derive from: `package.json`, `tsconfig.json`, `.eslintrc`, `vitest.config.*`, `next.config.*`, `vite.config.*`

- `npm run lint` or `eslint` passes with zero errors
- `tsc --noEmit` passes (typecheck only, no output)
- Bundle size within threshold if `bundlesize` or similar is configured
- No `console.log` in production source paths (allow in test files)
- No `any` type cast count exceeding project baseline (if strict mode is declared intent)
- `npm audit --audit-level=high` exits 0 (or equivalent)
- Required env vars declared in `.env.example` are all present in actual env
- No hardcoded localhost URLs in non-test source files

### Python

Derive from: `pyproject.toml`, `setup.py`, `requirements*.txt`, `ruff.toml`, `.flake8`, `pytest.ini`, `tox.ini`

- `ruff check` or `flake8` passes
- `mypy` or `pyright` passes if type checking is configured
- `pytest` exits 0
- No `print()` in production source paths (allow in scripts and tests)
- No bare `except:` clauses (require `except Exception` or narrower)
- No TODO/FIXME in public API surface files if project policy says so
- Dependencies in `requirements.txt` are pinned (warn if unpinned)

### .NET / C#

Derive from: `.csproj`, `.sln`, `global.json`, `dotnet-tools.json`, `.editorconfig`

- `dotnet build` exits 0 with no warnings if `TreatWarningsAsErrors` is set
- `dotnet test` exits 0
- No C# language version higher than declared in `<LangVersion>` (e.g., no record types if `langversion=8`)
- No `MessageBox.Show` or direct UI thread marshalling outside designated UI layers
- No hardcoded UI strings in `.cs` files when a resource/localization system is in use
- XAML files contain no non-ASCII characters in localizable text nodes if i18n is required
- No `.cs` files missing from their `.csproj` (detect orphaned files)
- Emoji characters absent from non-documentation source files
- Log statements use the declared logging abstraction, not `Console.Write` or `Debug.Print`
- `dotnet format --verify-no-changes` exits 0 if formatting is enforced

### Go

Derive from: `go.mod`, `go.sum`, `.golangci.yml`, `Makefile`

- `go build ./...` exits 0
- `go test ./...` exits 0
- `go vet ./...` exits 0
- `golangci-lint run` exits 0 if configured
- No `panic()` calls outside bootstrap or intentional crash handlers
- `go mod tidy` produces no diff (dependency graph is clean)

### Rust

Derive from: `Cargo.toml`, `Cargo.lock`, `clippy.toml`, `.cargo/config.toml`

- `cargo build` exits 0
- `cargo test` exits 0
- `cargo clippy -- -D warnings` exits 0
- `cargo fmt --check` exits 0
- No `unwrap()` calls in library public API surface (allow in tests and binaries with clear justification)
- `cargo audit` exits 0 if configured

### Mobile — iOS / Swift

Derive from: `Package.swift`, `.xcodeproj`, `Podfile`, `Fastfile`

- `xcodebuild test` exits 0 for the primary scheme
- `swiftlint` exits 0 if configured
- No force unwrap `!` in non-test production Swift files above a declared threshold
- No hardcoded production API URLs in source
- Signing config uses provisioning profiles from the declared source, not ad-hoc overrides

### Mobile — Android / Kotlin

Derive from: `build.gradle`, `settings.gradle`, `gradle.properties`, `lint.xml`

- `./gradlew build` exits 0
- `./gradlew test` exits 0
- `./gradlew lint` exits 0 with no errors
- No hardcoded strings in Kotlin/Java files when `strings.xml` is the declared source
- No API keys or secrets in `local.properties` committed to source

### CLI / Tooling

Derive from: manifest files, `Makefile`, shell scripts, `Dockerfile`

- Primary build target exits 0
- Unit tests exit 0
- Help flag (`--help`) exits 0 and produces non-empty output
- Shell scripts pass `shellcheck` if configured
- No hardcoded absolute paths in source that break cross-platform use
- Version string in source matches declared version in manifest

### Library / SDK

Derive from: manifest, `CHANGELOG.md`, examples directory, docs site config

- `make docs` or equivalent doc build exits 0
- All public API examples in `examples/` or `README.md` compile and run
- Breaking changes in public API surface are flagged if a semver policy is declared
- No internal implementation symbols exported from the public API surface

---

## Project Gates

Derive these from what inspection reveals. Add them only when evidence exists — do not invent checks.

| Evidence Found | Gate to Add |
|---|---|
| Localization/i18n system (resource files, `.resx`, `i18n/`, `locale/`) | Hardcoded localizable strings absent from non-template source |
| Logging abstraction declared in AGENTS.md or rules | Log calls use abstraction, not raw output |
| SVN / custom VCS wrapper in repo | Auth parameters follow declared format |
| State file accessed by multiple modules (e.g. `last_state.json`) | Only approved accessors touch that file |
| Test count baseline in CI or script | Test count does not drop below baseline |
| File length limit declared in rules | No source file exceeds declared line limit |
| Forbidden API list in AGENTS.md or `.codex/rules/` | Scan source for each forbidden call |
| Emoji policy in AGENTS.md | Emoji absent from declared file types |
| Shared rule or config files that must stay in sync across directories | Checksum or diff comparison between copies |
| `.csproj` completeness rule | Every `.cs` file appears in its project file |

---

## Script Shape

### Shell (`scripts/verify.sh`)

```bash
#!/usr/bin/env bash
set -euo pipefail

PASS=0
FAIL=0

run_check() {
  local name="$1"; shift
  if "$@" > /dev/null 2>&1; then
    echo "  [PASS] $name"
    ((PASS++))
  else
    echo "  [FAIL] $name"
    ((FAIL++))
  fi
}

echo "=== Build ==="
run_check "compile" <build command>

echo "=== Tests ==="
run_check "unit tests" <test command>

echo "=== Lint ==="
run_check "lint" <lint command>

echo "=== Project Gates ==="
run_check "no hardcoded secrets" grep -rn "API_KEY\s*=" src/
# add project-specific checks here

echo ""
echo "Result: $PASS passed, $FAIL failed"
[ "$FAIL" -eq 0 ]
```

### PowerShell (`scripts/verify.ps1`)

```powershell
$pass = 0; $fail = 0

function Run-Check {
  param([string]$Name, [scriptblock]$Block)
  try {
    & $Block | Out-Null
    Write-Host "  [PASS] $Name" -ForegroundColor Green
    $script:pass++
  } catch {
    Write-Host "  [FAIL] $Name" -ForegroundColor Red
    $script:fail++
  }
}

Write-Host "=== Build ==="
Run-Check "compile" { <build command> }

Write-Host "=== Tests ==="
Run-Check "unit tests" { <test command> }

Write-Host "=== Lint ==="
Run-Check "lint" { <lint command> }

Write-Host "=== Project Gates ==="
# add project-specific checks here

Write-Host "`nResult: $pass passed, $fail failed"
if ($fail -gt 0) { exit 1 }
```

---

## What to Record in `docs/harness.md`

After generating the script, update the Verification section of `docs/harness.md` to list:

- Unified entrypoint path
- Each gate name and the command it wraps
- Any gates deferred to a future iteration with a reason
- Known blockers (e.g. "build command not yet configured — script exits with a clear message")
