# Changelog

## [2.0.0] - 2026-09-05

### Changed
- **BREAKING**: Migrated to OWASP Top 10 for LLM Applications 2026. Category IDs renumbered/renamed: Excessive Agency → LLM03, Supply Chain → LLM04, Data and Model Poisoning → LLM05, Unbounded Consumption → LLM06, Misinformation → LLM07, Improper Output Handling → LLM10.
- LLM07 System Prompt Leakage re-scoped into **LLM08 Hidden Context Exposure**, now severity-graded and reported per-skill (informational when no secrets or behavioral-control logic leak) instead of a fixed-Medium systemic finding.

### Added
- Detection guidance and inventory cues for **Unbounded Consumption (LLM06)** (uncapped tool-call fan-out, downstream-command chaining) and **Misinformation (LLM07)** (guess/auto-fill/skip-human-review patterns).

## [1.0.0] - 2026-04-22

### Added
- Initial release
- `/speckit.threatmodel.analyze` command for OWASP LLM Top 10 2025 threat analysis
- `threat-model-{YYYY-MM-DD}-{NNN}.md` output with risk ratings and mitigations (date-stamped, auto-sequenced)
- `after_implement` hook for optional post-implementation security review
- Support for scoped analysis (all skills, single skill, single file)
