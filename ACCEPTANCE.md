# Acceptance — Corley Company Mapping Skill

## Structural
- [ ] SKILL.md has valid YAML frontmatter with `name` and `description`.
- [ ] `description` lists explicit trigger phrases (IT + EN).
- [ ] SKILL.md ≤ 500 lines.
- [ ] Every reference file is loaded by name somewhere in SKILL.md.
- [ ] No file contains TBD/TODO/placeholder text.
- [ ] Output is a single self-contained HTML file (inline CSS, no external assets) with summary table + per-sector cards.
- [ ] A PDF is generated from the HTML via headless Chrome, preserving layout, colors, and the org chart.
- [ ] CRM-present contacts show email and phone (or "n.d." when phone absent); every cited person links to LinkedIn when a verified profile exists.
- [ ] Org chart renders as tiered Decide/Influenza/Usa with a starred recommended contact, ghost nodes, and a path line.

## Behavioral (verified on the sample run, Task 11)
- [ ] Phase 1 CRM cross-reference runs BEFORE any web search.
- [ ] Each company is classified warm/cold with CRM evidence (event + year + notes) when warm.
- [ ] Every number carries [Dato]/[Stima]/[Ipotesi] and a date.
- [ ] Stale figures (>12-18 months) are flagged.
- [ ] Data gaps are declared explicitly with "how to verify"; no fabricated values.
- [ ] Source conflicts are reconciled out loud.
- [ ] People are classified decisore/influencer/utente.
- [ ] Deep tier produces 2-3 authentic ganci per priority contact, passing the creepy test.
- [ ] Each scheda ends with a clickable sources list.
- [ ] Verification pass runs before output and reports critical issues if any.
