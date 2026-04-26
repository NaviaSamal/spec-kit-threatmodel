# OWASP LLM Threat Model — Spec Kit Extension

OWASP Top 10 for LLM Applications 2025 threat analysis for Spec Kit workspaces.

## What It Does

This extension scans your Spec Kit workspace artifacts — skills files — and analyzes them against the [OWASP Top 10 for LLM Applications 2025](https://owasp.org/www-project-top-10-for-large-language-model-applications/) framework. It produces a structured threat model report with risk ratings (Likelihood × Impact) and recommended mitigations, all without modifying any existing files.

## Installation

**By name** (after catalog PR merges):
```bash
specify extension add threatmodel
```

**Direct from release** (works immediately):
```bash
specify extension add threatmodel --from https://github.com/NaviaSamal/spec-kit-threatmodel/archive/refs/tags/v1.0.0.zip
```

**Dev mode** (local testing):
```bash
specify extension add --dev /path/to/spec-kit-threatmodel
```

## Usage

```
/speckit.threatmodel.analyze
```

**Scan all artifacts** (skills, templates, memory):
```
/speckit.threatmodel.analyze
```

**Scan a specific skill**:
```
/speckit.threatmodel.analyze speckit-specify
```

**Scan a single file**:
```
/speckit.threatmodel.analyze .claude/skills/my-skill/SKILL.md
```

## Output Files

| File | Description |
|------|-------------|
| `FEATURE_DIR/threat-model-{YYYY-MM-DD}-{NNN}.md` | Full threat analysis with risk ratings and mitigations per OWASP category |

## OWASP LLM Top 10 2025 Categories

| ID | Category | Spec-Kit Context |
|----|----------|------------------|
| LLM01 | Prompt Injection | arguments if passed unsanitized to instructions |
| LLM02 | Sensitive Information Disclosure | API keys, PII, secrets in templates or memory |
| LLM03 | Supply Chain | External skill dependencies, untrusted sources |
| LLM04 | Data and Model Poisoning | User-controlled RAG/embedding content |
| LLM05 | Improper Output Handling | Skill output executed without validation |
| LLM06 | Excessive Agency | Auto-execution without confirmation gates |
| LLM07 | System Prompt Leakage | Instructions or prompts exposed in output |
| LLM08 | Vector and Embedding Weaknesses | Unvalidated RAG data, cross-tenant access |
| LLM09 | Misinformation | Skills that suppress human review, unverified claims |
| LLM10 | Unbounded Consumption | Recursive skill invocation, resource exhaustion |

## Risk Matrix

Risk is calculated as **Likelihood × Impact**:

|               | Low Impact | Medium | High | Critical |
|---------------|------------|--------|------|----------|
| High Likelih. | Medium     | High   | Crit | Crit     |
| Med Likelih.  | Low        | Medium | High | High     |
| Low Likelih.  | Low        | Low    | Med  | Medium   |

**Blocking Threats** (Critical risk) are listed at the top of the report and must be resolved before deployment.

## Example Output

```markdown
# Threat Model: all skills

**Date**: 2026-04-22T10:00:00Z
**Scope**: all skills
**Methodology**: OWASP LLM Top 10 2025

## Blocking Threats ⚠️

None identified

## Threats by Category

### LLM01: Prompt Injection
- **THR-01-001**: filename - Uses unescaped arguments in shell command
  Likelihood: High | Impact: High | Risk: Critical
  - Mitigation: Wrap arguments in quotes and validate against an allowlist before passing to shell

## Analysis Metadata
- Artifacts analyzed: 12
- Threats identified: 3
- Critical: 0 | High: 1 | Medium: 2 | Low: 0
```

## Hook Integration

The extension registers an optional `after_implement` hook. After each `/speckit.implement`, you'll be prompted:

```
Run OWASP LLM threat analysis on this feature?
To execute: /speckit.threatmodel.analyze
```

## License

MIT — Copyright (c) 2026 NaviaSamal
