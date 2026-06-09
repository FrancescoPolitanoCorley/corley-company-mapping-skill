# Acceptance — Corley Company Mapping Skill

## Structural
- [ ] SKILL.md has valid YAML frontmatter with `name` and `description`.
- [ ] `description` lists explicit trigger phrases (IT + EN).
- [ ] SKILL.md ≤ 500 lines.
- [ ] Every reference file is loaded by name somewhere in SKILL.md.
- [ ] No file contains TBD/TODO/placeholder text.
- [ ] Output is a single self-contained HTML file (inline CSS, no external assets) with summary table + per-sector cards (summary table omitted for single-company dossiers, body gets class="single").
- [ ] A PDF is generated from the HTML via headless Chrome, preserving layout, colors, and the org chart.
- [ ] CRM-present contacts show email and phone (or "n.d." only when the sheet cell is actually empty); every cited person links to LinkedIn when a verified profile exists.
- [ ] Org chart renders as tiered Decide/Influenza/Usa with a starred recommended contact, ghost nodes, and a path line.
- [ ] Output files follow the naming convention (single company: `{id}.html`; list: `company-mapping-{YYYY-MM-DD}.html`; PDF same basename), unless the user overrides it.

## Behavioral (verified on a sample run: 1 company Standard + 1 company Deep, any company)
- [ ] Phase 1 CRM cross-reference runs BEFORE any web search.
- [ ] Each company is classified warm/cold with CRM evidence (event + year + notes) when warm.
- [ ] Every number carries [Dato]/[Stima]/[Ipotesi] and a date.
- [ ] Stale figures (>12-18 months) are flagged.
- [ ] Data gaps are declared explicitly with "how to verify"; no fabricated values.
- [ ] Source conflicts are reconciled out loud.
- [ ] People are classified decisore/influencer/utente.
- [ ] Deep tier produces 2-3 ganci per priority contact, each citing a public source and a stated usage (apertura / qualifica / call); rejected hooks listed with reasons. The dossier text never names internal jargon (tier, wave, creepy test).
- [ ] Each scheda ends with a clickable sources list.
- [ ] A verification report file is written before rendering (even in the no-sub-agent fallback); critical issues stop the run.
- [ ] Each synthesized company is appended to the local `company-mapping-db.csv` (created with header if missing) only after verification passes, with `contatti_json`/`fonti_json` and inline labels.
- [ ] HTML/PDF can be regenerated from `company-mapping-db.csv` (latest row per id, last-appended wins on same date) without re-running phases 1-4.
- [ ] A Deep run on a company already covered at a lower tier writes raw files to `raw-deep/{company}/` (previous raw preserved), re-verifies contact fields at the CRM source, and re-attempts ghost nodes (promoting confirmed ones to full nodes).
