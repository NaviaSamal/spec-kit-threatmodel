# Changelog

## [2.1.2] - 2026-09-09

### Fixed
- **LLM08 Hidden Context Exposure — Informational disposition**: plain workflow skills (step ordering, iteration caps, clarification rules, naming conventions) no longer produce a Low THR-ID finding. LLM08 now uses a four-state disposition aligned with the OWASP 2026 severity scale: Threat found (Medium+) → THR-ID assigned; No threat detected → applicable but clean; **Informational** → only plain workflow instructions, no THR-ID, excluded from threat counts and risk matrix; N/A → never used for LLM08. Only credentials/tokens, authorization logic, refusal/content-policy rules, or privilege-exposing tool schemas trigger a finding. This eliminates false-positive Low findings on skills that contain no security-relevant hidden context.
- **LLM04 Supply Chain — scope correction**: the `.specify/extensions.yml` hook-dispatch pattern is no longer flagged as LLM04. That file is written and managed by the `specify` CLI at `extension add` time; it is a user-consented install-time registry, not an attacker-controlled surface. The OWASP 2026 PDF explicitly scopes LLM04 to ML artifacts (models, datasets, adapters, conversion pipelines) and redirects agentic tool-registry risks to ASI04. LLM04 detection now focuses on real runtime supply-chain surfaces: skill instructions to `pip install`, `npm install`, `curl | bash`, or load external model artifacts where package/URL names may be attacker-influenced or LLM-hallucinated (slopsquatting risk).
- **LLM03 Excessive Agency — impact calibration**: when a skill has mandatory hooks that auto-execute (`optional: false`) without a user confirmation gate AND the same skill has a confirmed LLM01 instruction-interpolation finding, LLM03 Impact is now rated High (not Medium) — the injection blast radius reaches the hook execution path, making the effective threat Medium × High = High.

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
