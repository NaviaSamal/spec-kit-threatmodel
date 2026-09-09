# Changelog

## [2.1.1] - 2026-09-06

### Fixed
- **LLM01 Prompt Injection — usage-pattern classification**: the analyzer now distinguishes three `$ARGUMENTS` usage patterns and sets Likelihood accordingly: *instruction-interpolation* (argument embedded in instruction prose or file-path constructions) → High; *API/tool parameter* (argument passed to an API/tool call, no prose interpolation) → Medium, or No threat detected if format/allowlist validation is present; *scope selector only* (argument used only to select what to scan) → No threat detected. Previously all three patterns triggered the same High finding, producing noise on skills that use `$ARGUMENTS` only for routing. Additionally, the analyzer no longer downgrades LLM01 Likelihood because the skill text claims the argument is "attacker-controlled" or "trusted" — those are documentation, not mitigations.

## [2.1.0] - 2026-09-06

### Added
- **Applicability gating (three-state disposition)**: each category now resolves to a threat finding, `No threat detected.` (applicable and clean), or `N/A — {reason}` (structurally not applicable to a single `SKILL.md`). The four gateable categories — LLM05 (Data and Model Poisoning), LLM06 (Unbounded Consumption), LLM09 (Vector and Embedding Weaknesses), and LLM10 (Improper Output Handling) — are evaluated only when their applicability surface is present; otherwise they are marked `N/A` and excluded from threat counts, the risk matrix, and the blocking set. The other six categories (LLM01–04, LLM07, LLM08) remain always-evaluated. This removes misleading "No threat detected" output for categories that could never fire.

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
