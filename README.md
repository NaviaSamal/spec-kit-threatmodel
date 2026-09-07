# OWASP LLM Threat Model — Spec Kit Extension

OWASP Top 10 for LLM Applications 2026 threat analysis for Spec Kit workspaces.

## What It Does

This extension scans your Spec Kit workspace artifacts — skills files — and analyzes them against the [OWASP Top 10 for LLM Applications 2026](https://genai.owasp.org/llm-top-10/) framework. It produces a structured threat model report with risk ratings (Likelihood × Impact) and recommended mitigations, all without modifying any existing files.

## Installation

**By name** (after catalog PR merges):
```bash
specify extension add threatmodel
```

**Direct from release** (works immediately):
```bash
specify extension add threatmodel --from https://github.com/NaviaSamal/spec-kit-threatmodel/archive/refs/tags/v2.1.0.zip
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

Each category in the report shows one of three dispositions: a **threat finding**, **`No threat detected.`** (applicable, checked, clean), or **`N/A — {reason}`** (the category is structurally not applicable to a single `SKILL.md`, so it is not evaluated and contributes nothing to counts, the risk matrix, or the blocking set). See the Applicability column below.

## OWASP LLM Top 10 2026 Categories

| ID | Category | Spec-Kit Context | Applicability |
|----|----------|------------------|---------------|
| LLM01 | Prompt Injection | arguments if passed unsanitized to instructions | Always |
| LLM02 | Sensitive Information Disclosure | API keys, PII, secrets in templates or memory | Always |
| LLM03 | Excessive Agency | Auto-execution without confirmation gates, excessive tool permissions/autonomy | Always |
| LLM04 | Supply Chain | External skill dependencies, untrusted sources | Always |
| LLM05 | Data and Model Poisoning | User-controlled RAG/embedding content | Conditional — only if the skill writes user/external input to a persistent/memory/RAG-feeding file |
| LLM06 | Unbounded Consumption | Recursive skill invocation, uncapped fan-out, resource exhaustion | Conditional — only if the skill has loops, recursion, self-invocation, or tool-call fan-out |
| LLM07 | Misinformation | Skills that suppress human review, auto-fill/guess patterns, unverified claims | Always |
| LLM08 | Hidden Context Exposure | Secrets, refusal rules, or behavioral logic embedded in readable context files | Always |
| LLM09 | Vector and Embedding Weaknesses | Unvalidated RAG data, cross-tenant access | Conditional — only if the skill declares a vector store / embedding / RAG retrieval path |
| LLM10 | Improper Output Handling | Skill output executed without validation | Conditional — only if the skill's output flows into a shell/SQL/path/template sink |

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
**Methodology**: OWASP LLM Top 10 2026

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
