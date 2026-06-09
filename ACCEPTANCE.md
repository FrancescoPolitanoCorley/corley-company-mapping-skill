# Acceptance — Corley Company Mapping Skill

## Structural
- [ ] SKILL.md has valid YAML frontmatter with `name` and `description`.
- [ ] `description` lists explicit trigger phrases (IT + EN).
- [ ] SKILL.md ≤ 500 lines.
- [ ] Every reference file is loaded by name somewhere in SKILL.md.
- [ ] No file contains TBD/TODO/placeholder text.
- [ ] Output is a single self-contained HTML file (inline CSS, no external assets) with summary table + per-sector cards (summary table omitted for single-company dossiers, body gets class="single").
- [ ] A PDF is generated from the HTML via headless Chrome, preserving layout, colors, and the org chart.
- [ ] Contacts present in the Drive lead lists show email and phone (or "n.d." only when the sheet cell is actually empty); every cited person links to LinkedIn when a verified profile exists.
- [ ] Org chart renders as tiered Decide/Influenza/Usa with a starred recommended contact, ghost nodes, and a path line.
- [ ] The mossa/azione is phrased as a proposal: no internal sender prescribed, and NO deadlines or relative time references ("entro venerdì", "questa settimana") — the dossier must not age. Absolute dates on facts are fine.
- [ ] Output files follow the naming convention (single company: `{id}.html`; list: `company-mapping-{YYYY-MM-DD}.html`; PDF same basename), unless the user overrides it.

## Behavioral (verified on a sample run: 1 company Standard + 1 company Deep, any company)
- [ ] Phase 0 opens with a multiple-choice Standard/Deep question stating depth and cost implications (skipped only if the user already specified the tier; defaults to Standard when no answer is possible).
- [ ] Phase 1 lead-list cross-reference (Drive) runs BEFORE any web search.
- [ ] Each company is classified warm/cold (or NON VERIFICATO when lists are unreachable) with lead-list evidence when warm: source file named (e.g. "lead AWS Summit 2026") + event + year + notes. The dossier never calls the lists "CRM".
- [ ] Every number carries [Dato]/[Stima]/[Ipotesi] and a date.
- [ ] Stale figures (>12-18 months) are flagged.
- [ ] Data gaps are declared explicitly with "how to verify"; no fabricated values.
- [ ] Source conflicts are reconciled out loud.
- [ ] People are classified decisore/influencer/utente.
- [ ] Deep tier produces 2-3 ganci per priority contact, each citing a public source and a stated usage (apertura / qualifica / call); rejected hooks listed with reasons. The dossier text never names internal jargon (tier, wave, creepy test).
- [ ] Each scheda ends with a clickable sources list.
- [ ] A verification report file is written before rendering (even in the no-sub-agent fallback); critical issues stop the run.
- [ ] On lists larger than ~8 companies, work proceeds in batches of 5-8 (phases 2-4 per batch, verified rows appended per batch, PROGRESS updated per batch); rendering happens once at the end from the CSV.
- [ ] Each synthesized company is appended to the local `company-mapping-db.csv` (created with header if missing) only after verification passes, with `contatti_json`/`fonti_json` and inline labels.
- [ ] HTML/PDF can be regenerated from `company-mapping-db.csv` (latest row per id, last-appended wins on same date) without re-running phases 1-4.
- [ ] A Deep run on a company already covered at a lower tier writes raw files to `raw-deep/{company}/` (previous raw preserved), re-verifies contact fields on the lead sheets, and re-attempts ghost nodes (promoting confirmed ones to full nodes).
