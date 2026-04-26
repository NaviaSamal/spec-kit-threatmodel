# CLAUDE.md — spec-kit-threatmodel

## Project Identity

This is a **Spec Kit extension** called `threatmodel`. It performs OWASP Top 10 for LLM Applications 2025 threat analysis on agent artifacts (skills, prompts, templates, hooks, memory files) in Spec Kit workspaces.

- **Extension ID**: `threatmodel`
- **Author**: NaviaSamal
- **Repository**: `https://github.com/NaviaSamal/spec-kit-threatmodel`
- **License**: MIT

## Starting Point (Recommended)

Before scaffolding from scratch, clone the **official Spec Kit extension template** as the baseline:

```bash
# Option A: Copy the template directory from a spec-kit clone
git clone --depth 1 https://github.com/github/spec-kit.git /tmp/spec-kit
cp -r /tmp/spec-kit/extensions/template ./spec-kit-threatmodel
rm -rf /tmp/spec-kit
cd spec-kit-threatmodel
rm -rf .git
```

This guarantees alignment with whatever the maintainers currently expect. Then customize per the tasks below.

Reference docs (all under `https://github.com/github/spec-kit/tree/main/extensions/`):
- `EXTENSION-DEVELOPMENT-GUIDE.md` — how to build
- `EXTENSION-API-REFERENCE.md` — manifest schema and hooks
- `EXTENSION-PUBLISHING-GUIDE.md` — how to submit to the catalog
- `EXTENSION-USER-GUIDE.md` — how end users install and use extensions

## What This Extension Does

When invoked via `/speckit.threatmodel.analyze`, it:
1. Scans all skills, templates, hooks, and memory files in a Spec Kit workspace
2. Analyzes each artifact against the OWASP Top 10 for LLM Applications 2025 framework
3. Produces `FEATURE_DIR/threat-model.md` with categorized threats, risk ratings (Likelihood × Impact), and recommended mitigations

The command is **strictly read-only** — it analyzes but never modifies existing files.

## OWASP LLM Top 10 2025 Categories

| ID | Category | Spec-Kit Context |
|----|----------|------------------|
| LLM01 | Prompt Injection | `$ARGUMENTS` passed unsanitized to instructions |
| LLM02 | Sensitive Information Disclosure | API keys, PII, secrets in templates or memory |
| LLM03 | Supply Chain | External skill dependencies, untrusted sources |
| LLM04 | Data and Model Poisoning | User-controlled RAG/embedding content |
| LLM05 | Improper Output Handling | Skill output executed without validation |
| LLM06 | Excessive Agency | Auto-execution without confirmation gates |
| LLM07 | System Prompt Leakage | Instructions or prompts exposed in output |
| LLM08 | Vector and Embedding Weaknesses | Unvalidated RAG data, cross-tenant access |
| LLM09 | Misinformation | Skills that suppress human review, unverified claims |
| LLM10 | Unbounded Consumption | Recursive skill invocation, resource exhaustion |

## Required Extension Structure

Matches the official spec-kit `extensions/template/` layout:

```
spec-kit-threatmodel/
├── CLAUDE.md                  ← This file (agent instructions; excluded by .extensionignore)
├── extension.yml              ← Extension manifest (REQUIRED)
├── config-template.yml        ← Configuration template (present even if empty)
├── README.md                  ← Documentation (REQUIRED)
├── LICENSE                    ← MIT license (REQUIRED)
├── CHANGELOG.md               ← Version history
├── .extensionignore           ← Exclude dev files from install
├── .gitignore
└── commands/
    └── analyze.md             ← Main command file (REQUIRED)
```

Note: the command file is `commands/analyze.md` (not `threatmodel.md`) because spec-kit's command-naming convention is `speckit.{extension-id}.{command}` — the extension id is already `threatmodel`, so the file represents the subcommand.

## Build Tasks

When asked to "build the extension" or "scaffold the extension", perform these tasks in order.

### Task 1: Create `extension.yml`

Follow this exact schema (aligned with the official template and API reference):

```yaml
schema_version: "1.0"

extension:
  id: "threatmodel"
  name: "OWASP LLM Threat Model"
  version: "1.0.0"
  description: "OWASP Top 10 for LLM Applications 2025 threat analysis on agent artifacts"
  author: "NaviaSamal"
  repository: "https://github.com/NaviaSamal/spec-kit-threatmodel"
  license: "MIT"
  homepage: "https://github.com/NaviaSamal/spec-kit-threatmodel"

requires:
  speckit_version: ">=0.1.0"

provides:
  commands:
    - name: "speckit.threatmodel.analyze"
      file: "commands/analyze.md"
      description: "Run OWASP LLM Top 10 2025 threat analysis on workspace artifacts"

hooks:
  after_implement:
    command: "speckit.threatmodel.analyze"
    optional: true
    prompt: "Run OWASP LLM threat analysis on this feature?"
    description: "Post-implementation security review"

tags:
  - "security"
  - "owasp"
  - "threat-model"
  - "llm"
  - "analysis"
```

Validation rules:
- `id` must be lowercase with hyphens only (no underscores, spaces, special chars)
- `version` must be semantic (X.Y.Z)
- Command names MUST follow `speckit.{extension-id}.{subcommand}` pattern — verb-noun subcommands preferred (`analyze`, `scan`, `run`)
- All referenced command files must exist at the declared `file` path
- Hook event names use **underscores**: `after_plan`, `after_tasks`, `after_implement` (confirmed in `templates/commands/plan.md` upstream)

### Task 2: `commands/analyze.md` — RENAME + UPDATE FROM UPSTREAM

The original file exists in the feature branch of the fork as `templates/commands/threatmodel.md`. For the extension, it gets renamed to `commands/analyze.md` and then edited in several specific places.

**Important — two files are easy to confuse, don't:**
- `commands/analyze.md` — the command PROMPT (what the agent reads when `/speckit.threatmodel.analyze` runs). **This is what we rename and edit.**
- `threat-model.md` — the OUTPUT report that the command produces at runtime in `FEATURE_DIR/threat-model.md`. **This name stays; don't rename it.**

#### Step 2a — Get the file

```bash
# From a fresh checkout of the new extension repo root:
curl -o commands/analyze.md \
  https://raw.githubusercontent.com/NaviaSamal/spec-kit/feat/add-threat-model-skill/templates/commands/threatmodel.md

# OR if the fork is cloned locally at ../spec-kit:
cp ../spec-kit/templates/commands/threatmodel.md commands/analyze.md
```

#### Step 2b — Required edits inside `commands/analyze.md`

Walk through the file and apply these changes. **Do not regenerate the OWASP analysis logic, risk matrix, or output templates** — those stay. Only the metadata and naming change.

1. **Frontmatter — merge in the argument-hint.** In the original core PR, the argument hint was added to `src/specify_cli/integrations/claude/__init__.py` (the `ARGUMENT_HINTS` dict). Extensions can't modify that file, so the hint must move into the command's own frontmatter:
   ```yaml
   ---
   description: "Run OWASP LLM Top 10 2025 threat analysis on workspace artifacts"
   argument-hint: "Optional focus areas or specific OWASP LLM categories to analyze"
   ---
   ```
   Keep any additional frontmatter fields from the original (e.g., `scripts:` references) as-is.

2. **Command self-references.** Every mention of `/speckit.threatmodel` inside the body — in instructions, usage examples, error messages, output snippets — becomes `/speckit.threatmodel.analyze`. Search-and-replace, but review each hit since some may be referring to the output filename `threat-model.md` (which stays).

3. **Agent-agnostic language.** The Dev Guide requires commands to work across Claude, Copilot, Gemini, and other supported agents. Audit for:
   - "Claude should..." → "The agent should..." (or "You should...", matching the style spec-kit core commands use)
   - Claude-specific tool names or affordances → generalize to the neutral equivalent
   - Any file paths hardcoded to `.claude/` → use the spec-kit-provided abstraction instead

4. **Prerequisite script calls.** If the file invokes `check-prerequisites.sh` or other core scripts, confirm they're still called via relative paths that work from an extension context. Spec-kit core scripts live at `.specify/scripts/bash/` and `.specify/scripts/powershell/` inside the user's initialized project, so references should use that path (not a path relative to the extension's own directory).

5. **Output path references.** Mentions of `FEATURE_DIR/threat-model.md` STAY. This is the artifact your extension produces — the name `threat-model.md` is part of your extension's contract and is unrelated to the `analyze` subcommand name.

6. **Remove any test/CI hooks** that assumed the file lived at `templates/commands/threatmodel.md` in spec-kit core. Those tests belong in the old PR, not this file.

#### Step 2c — Verification

After editing, sanity-check:
- `grep -n "speckit.threatmodel[^.]" commands/analyze.md` should return no hits (every occurrence should be followed by `.analyze` or similar)
- `grep -n "threat-model.md" commands/analyze.md` should still return hits (this is the output filename, unchanged)
- The frontmatter block parses as valid YAML
- No references to `ARGUMENT_HINTS`, `src/specify_cli/`, or the `tests/integrations/` files from the core PR remain

### Task 3: Create `config-template.yml`

The official template includes this file. `/speckit.threatmodel.analyze` doesn't currently require user config, so create a minimal placeholder that leaves room for future settings:

```yaml
# Configuration template for the threatmodel extension.
# Currently no user-facing configuration is required.
# Future options may include:
#   - Custom OWASP category overrides
#   - Severity threshold for blocking threats
#   - Output directory customization

threatmodel:
  # Placeholder — no active settings in v1.0.0
  enabled: true
```

### Task 4: Create `README.md`

Include:
- Extension name and one-line description
- What it does (one paragraph)
- Installation (show BOTH methods):
  - By name (after catalog PR merges): `specify extension add threatmodel`
  - Direct from release (works immediately): `specify extension add threatmodel --from https://github.com/NaviaSamal/spec-kit-threatmodel/archive/refs/tags/v1.0.0.zip`
  - Dev mode for local testing: `specify extension add --dev /path/to/spec-kit-threatmodel`
- Usage: `/speckit.threatmodel.analyze` with examples (scan all artifacts, scan a single skill)
- Output files description (`threat-model.md`)
- OWASP LLM Top 10 2025 categories table
- Risk matrix explanation
- Example output snippet
- License (MIT)

### Task 5: Create `LICENSE`

MIT license, copyright 2026 NaviaSamal.

### Task 6: Create `CHANGELOG.md`

```markdown
# Changelog

## [1.0.0] - 2026-04-22

### Added
- Initial release
- `/speckit.threatmodel.analyze` command for OWASP LLM Top 10 2025 threat analysis
- `threat-model.md` output with risk ratings and mitigations

- `after_implement` hook for optional post-implementation security review
- Support for scoped analysis (all skills, single skill, single file)
```

### Task 7: Create `.gitignore`

```
# OS files
.DS_Store
Thumbs.db

# Editor/IDE files
*.swp
*.swo
*~
.vscode/
.idea/
*.iml

# Sensitive/secret files
.env
.env.*
*.pem
*.key
*.secret
credentials.*

# Build artifacts
node_modules/
dist/
*.log
```

### Task 8: Create `.extensionignore`

Spec Kit supports `.extensionignore` (gitignore-compatible patterns) to exclude files from being copied when a user installs the extension. Development artifacts don't belong in user projects:

```
# Development-only files — excluded from user installs

# Agent instructions for this repo (not for end users)
CLAUDE.md

# Repo plumbing
.git/
.github/
.gitignore

# Dev docs and tests (if added later)
docs/
tests/
*.test.*

# Build artifacts
__pycache__/
*.pyc
dist/
```

## Pre-Publish Verification

Before committing or pushing, Claude Code MUST:

1. **Security sweep**:
   - Confirm `.gitignore` covers: `.env*`, `*.pem`, `*.key`, `*.secret`, `credentials.*`, `.vscode/`, `.idea/`, `.DS_Store`
   - Run `git status` and confirm NO sensitive files or IDE files are staged
   - If any are tracked, remove them: `git rm --cached <file>`

2. **Local install test** (the Dev Guide recommends this):
   ```bash
   # In a throwaway spec-kit project:
   specify extension add --dev /absolute/path/to/spec-kit-threatmodel
   specify extension list  # Verify: ✓ OWASP LLM Threat Model (v1.0.0)
   ```
   Then invoke `/speckit.threatmodel.analyze` in the agent and verify both output files are produced.

3. **Manifest sanity check**:
   - `extension.yml` parses as valid YAML
   - Every file referenced under `provides.commands[].file` exists
   - `hooks.*.command` references a command that's declared in `provides.commands`

## Publishing Tasks

When asked to "publish" or "release":

1. Commit everything: `git add -A && git commit -m "feat: OWASP LLM threat model extension v1.0.0"`
2. Create GitHub repo: `gh repo create NaviaSamal/spec-kit-threatmodel --public --description "OWASP Top 10 for LLM Applications threat analysis for Spec Kit" --source . --push`
3. Tag release: `git tag v1.0.0 && git push origin main --tags`
4. Create GitHub release: `gh release create v1.0.0 --title "v1.0.0 - Initial Release" --notes "OWASP Top 10 for LLM Applications 2025 threat analysis for Spec Kit workspaces"`

The release archive URL will be:
`https://github.com/NaviaSamal/spec-kit-threatmodel/archive/refs/tags/v1.0.0.zip`

### Post-release smoke test

```bash
# In a fresh spec-kit project:
specify extension add threatmodel --from https://github.com/NaviaSamal/spec-kit-threatmodel/archive/refs/tags/v1.0.0.zip
specify extension list
# Run the command and confirm outputs
```

## Catalog Submission Tasks

When asked to "submit to catalog":

1. **Close the superseded core PR** (#2287 on `github/spec-kit`) with a comment linking to the new extension repo:
   ```bash
   gh pr close 2287 --repo github/spec-kit --comment "Superseded by community extension: https://github.com/NaviaSamal/spec-kit-threatmodel"
   ```

2. **Fork** `github/spec-kit` if not already forked; sync the fork's `main` with upstream.

3. **New branch** for the catalog entry (do NOT reuse the old feature branch):
   ```bash
   git checkout -b add-threatmodel-to-community-catalog
   ```

4. **Add entry to `extensions/catalog.community.json`** (alphabetical order, under key `threatmodel`). Set `verified: false`, `downloads: 0`, `stars: 0`. Update the top-level `updated_at`.

5. **Add row to Available Extensions table** in `extensions/README.md` — category `docs`, effect `Read-only` (since the command only produces new reports, it doesn't modify existing artifacts).

6. **Submit PR** with title: `Add threatmodel extension to community catalog` — describe the extension, link the repo + release, check off the submission checklist from the Publishing Guide.

## Constraints

- NEVER modify files in any local `../spec-kit/` directory (that's the upstream repo)
- NEVER commit secrets, API keys, or credentials — the security sweep in Pre-Publish Verification is mandatory
- Command files must remain agent-agnostic (work with Claude, Copilot, Gemini, and other supported agents)
- Follow the official Spec Kit extension conventions: https://github.com/github/spec-kit/blob/main/extensions/EXTENSION-DEVELOPMENT-GUIDE.md
- Prefer starting from the official template at `github/spec-kit/extensions/template/` over hand-scaffolding from this doc
