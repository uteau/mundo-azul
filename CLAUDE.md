# Mundo Azul

Desktop app (Tauri v2 + Rust + SQLite, React/TS/Tailwind) for a dive center's administrator, who uses it alone and offline to log each day's ventas, asignaciones, remuneraciones, costos and otros ingresos, and to analyse the business in a dashboard. No code exists yet: the work starts at ticket 01.

## Docs

- **Requirements** (RN/RF/RNF codes, decisions D-xx, open questions P-xx): `docs/01-requerimientos.md`. Read it before changing a business rule.
- **Design** (schema, views, API commands, frontend features): `docs/02-diseno.md`. Read it before adding a table, a command or a screen.
- **Tasks**: `docs/03-tareas.md` indexes the tickets in `.scratch/mundoazul-v1/issues/`. The feature spec is `.scratch/mundoazul-v1/spec.md`.
- `docs/archivo/` is history. Leave them untouched and take current truth from the docs above.

## Conventions

- **Language**: UI text, docs, tickets and domain identifiers are in Spanish, using the terms in `GLOSSARY.md` (guía, inasistencia, asignación, costo, otro ingreso). Dates show as `dd-mm-aaaa`; amounts are integer pesos.
- **Test seam**: test everything through the Rust service functions (they take a SQLite connection; Tauri commands only wrap them), on an in-memory database with all migrations applied. Assert business outcomes: returned values, rejected operations, report totals. The frontend is verified by hand.
- **Working a ticket**: take one whose blockers are all done, meet every acceptance criterion, then update the `Status:` line in the ticket file. The ticket file is the source of truth for status. Change `docs/03-tareas.md` only when tickets are added, split or re-blocked.
- **Decisions**: when a change contradicts a requirement or an ADR, update that document (or write a new ADR) in the same change.

## Agent skills

### Issue tracker

Issues and specs are tracked as local markdown files under `.scratch/<feature>/`. See `docs/agents/issue-tracker.md`.

### Triage labels

Default vocabulary (needs-triage, needs-info, ready-for-agent, ready-for-human, wontfix), recorded as a `Status:` line in each issue file. See `docs/agents/triage-labels.md`.

### Domain docs

Single-context: one `GLOSSARY.md` and `docs/adr/` at the repo root. See `docs/agents/domain.md`.
