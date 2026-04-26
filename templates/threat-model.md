# Threat Model: {{SCOPE}}

**Date**: {{DATE}}
**Scope**: {{SCOPE_DESCRIPTION}}
**Methodology**: OWASP Top 10 for LLM Applications 2025

## Blocking Threats

{{#if BLOCKING_THREATS}}
{{#each BLOCKING_THREATS}}
- **{{threat_id}}**: {{skill_names}} — {{one_sentence_description}} | Risk: **Critical**
{{/each}}

**{{BLOCKING_COUNT}} blocking threat(s) must be resolved before deployment.**
{{else}}
None identified.
{{/if}}

## Risk Matrix Summary

|               | Low Impact | Medium | High | Critical |
|---------------|------------|--------|------|----------|
| High Likelih. | {{HL_LI}}  | {{HL_MI}} | {{HL_HI}} | {{HL_CI}} |
| Med Likelih.  | {{ML_LI}}  | {{ML_MI}} | {{ML_HI}} | {{ML_CI}} |
| Low Likelih.  | {{LL_LI}}  | {{LL_MI}} | {{LL_HI}} | {{LL_CI}} |

## Threats Identified

{{#if THREATS_TABLE}}
**{{THREATS_COUNT}} total, {{BLOCKING_COUNT}} blocking**

| ID | Category | L × I | Risk |
|----|----------|-------|------|
{{#each THREATS_TABLE}}
| {{threat_id}} | {{category_name}} | {{likelihood_x_impact}} | {{risk}} |
{{/each}}
{{else}}
No threats identified.
{{/if}}

## Threats by Category

### LLM01: Prompt Injection

{{#if LLM01_THREATS}}
{{#each LLM01_THREATS}}
- **{{threat_id}}**: {{skill_names}} — {{description}}
  Likelihood: **{{likelihood}}** | Impact: **{{impact}}** | Risk: **{{risk}}**
  - Mitigation: {{mitigation}}

{{/each}}
{{else}}
No threat detected.
{{/if}}

### LLM02: Sensitive Information Disclosure

{{#if LLM02_THREATS}}
{{#each LLM02_THREATS}}
- **{{threat_id}}**: {{skill_names}} — {{description}}
  Likelihood: **{{likelihood}}** | Impact: **{{impact}}** | Risk: **{{risk}}**
  - Mitigation: {{mitigation}}

{{/each}}
{{else}}
No threat detected.
{{/if}}

### LLM03: Supply Chain

{{#if LLM03_THREATS}}
{{#each LLM03_THREATS}}
- **{{threat_id}}**: {{skill_names}} — {{description}}
  Likelihood: **{{likelihood}}** | Impact: **{{impact}}** | Risk: **{{risk}}**
  - Mitigation: {{mitigation}}

{{/each}}
{{else}}
No threat detected.
{{/if}}

### LLM04: Data and Model Poisoning

{{#if LLM04_THREATS}}
{{#each LLM04_THREATS}}
- **{{threat_id}}**: {{skill_names}} — {{description}}
  Likelihood: **{{likelihood}}** | Impact: **{{impact}}** | Risk: **{{risk}}**
  - Mitigation: {{mitigation}}

{{/each}}
{{else}}
No threat detected.
{{/if}}

### LLM05: Improper Output Handling

{{#if LLM05_THREATS}}
{{#each LLM05_THREATS}}
- **{{threat_id}}**: {{skill_names}} — {{description}}
  Likelihood: **{{likelihood}}** | Impact: **{{impact}}** | Risk: **{{risk}}**
  - Mitigation: {{mitigation}}

{{/each}}
{{else}}
No threat detected.
{{/if}}

### LLM06: Excessive Agency

{{#if LLM06_THREATS}}
{{#each LLM06_THREATS}}
- **{{threat_id}}**: {{skill_names}} — {{description}}
  Likelihood: **{{likelihood}}** | Impact: **{{impact}}** | Risk: **{{risk}}**
  - Mitigation: {{mitigation}}

{{/each}}
{{else}}
No threat detected.
{{/if}}

### LLM07: System Prompt Leakage

{{#if LLM07_THREATS}}
{{#each LLM07_THREATS}}
- **{{threat_id}}**: {{skill_names}} — {{description}}
  Likelihood: **{{likelihood}}** | Impact: **{{impact}}** | Risk: **{{risk}}**
  - Mitigation: {{mitigation}}

{{/each}}
{{else}}
No threat detected.
{{/if}}

### LLM08: Vector and Embedding Weaknesses

{{#if LLM08_THREATS}}
{{#each LLM08_THREATS}}
- **{{threat_id}}**: {{skill_names}} — {{description}}
  Likelihood: **{{likelihood}}** | Impact: **{{impact}}** | Risk: **{{risk}}**
  - Mitigation: {{mitigation}}

{{/each}}
{{else}}
No threat detected.
{{/if}}

### LLM09: Misinformation

{{#if LLM09_THREATS}}
{{#each LLM09_THREATS}}
- **{{threat_id}}**: {{skill_names}} — {{description}}
  Likelihood: **{{likelihood}}** | Impact: **{{impact}}** | Risk: **{{risk}}**
  - Mitigation: {{mitigation}}

{{/each}}
{{else}}
No threat detected.
{{/if}}

### LLM10: Unbounded Consumption

{{#if LLM10_THREATS}}
{{#each LLM10_THREATS}}
- **{{threat_id}}**: {{skill_names}} — {{description}}
  Likelihood: **{{likelihood}}** | Impact: **{{impact}}** | Risk: **{{risk}}**
  - Mitigation: {{mitigation}}

{{/each}}
{{else}}
No threat detected.
{{/if}}

## Analysis Metadata
- Skills analyzed: {{SKILLS_COUNT}}
- Threats identified: {{THREATS_COUNT}}
- Critical: {{CRITICAL_COUNT}} | High: {{HIGH_COUNT}} | Medium: {{MEDIUM_COUNT}} | Low: {{LOW_COUNT}}
- Generated: {{DATE}}