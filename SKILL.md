---
name: project-rounds
description: Cross-session continuity system for any project — per-project work logs with numbered Rounds, central CHANGELOG.md as the single index, memory rules that auto-update after every significant intervention. Use when working on long-running projects across multiple Claude Code sessions, when managing multiple projects/sites/clients in parallel, when continuity matters (i.e. losing the session shouldn't lose the thread), or when the user mentions "continuità", "non perdere il filo", "changelog", "session continuity", "work log", "round", "tracciabilità", "history", "ripartire da dove eravamo". Domain-agnostic: works for software development, SEO, ops/infra, content, design, research — any project where you want a Claude-readable history.
---

# project-rounds — session continuity for any project

A reusable workflow that makes Claude sessions **resilient to session loss**. Each project gets a per-project log organized as numbered **Rounds**; all projects share a central **CHANGELOG.md** as the cross-project index. After every significant intervention, both are updated — so a fresh session can rebuild full context in 30 seconds by reading two files.

## When to apply this skill

Activate when:
- The user is starting or working on a **long-running project** where multiple Claude sessions will be involved.
- The user manages **multiple projects / clients / sites in parallel** and needs cross-project tracking.
- The user mentions:
  - "**continuità**", "**non perdere il filo**", "**session continuity**"
  - "**changelog**", "**work log**", "**history**", "**tracciabilità**"
  - "**round**" applicato a iterazioni di lavoro
  - "**ripartire da dove eravamo**", "**ricostruire lo stato**"
- The user is **frustrated by lost context** between sessions.
- You're closing a session and **didn't update the log** for the work just done.

Do **not** activate for: one-shot questions, code review of small changes, exploratory chats where there's no project to track.

## The two files that matter

### 1. `<project-root>/work/CHANGELOG.md` — cross-project index

- Reverse-chronological (newest at top), grouped by date (`## YYYY-MM-DD`).
- One bullet per intervention. Cross-references the relevant `work/<item>.md`.
- **Index, not detail**: don't duplicate the content of per-item logs — link to them.

Example:
```markdown
## 2026-05-22

- **itemA** v1.2.0 → **v1.4.0**: <1-line summary>. Vedi `itemA.md` §Round 5.
- **itemB** redirect fix. Vedi `itemB.md` §Round 4.
- **diagnostica non-actionable** — alert "X" su itemB → confermato voluto. Vedi `itemB.md`.

## 2026-05-11

**Big push.**
- **itemA** ...
- **itemB** ...
```

### 2. `<project-root>/work/<item>.md` — per-item detail log

Anatomia canonica del log per ogni cosa tracciata (sito, modulo, cliente, deploy, area):

```markdown
# <Item Name> — Work Log

**Item:** <descrizione 1 riga>
**Path / URL:** <dove vive>
**Stack:** <tech stack rilevante>
**Access:** <come ci si arriva — SSH / FTP / API token>
**Credenziali:** <path .env locale, NON inline>
**Note iniziali:** <contesto, obiettivo>

---

## Diagnosi iniziale (YYYY-MM-DD)

<stato di partenza, metriche prima dell'intervento, problemi rilevati>

---

## Round 1 (YYYY-MM-DD) — <titolo>

### Driver
<perché è stato fatto questo round>

### Operazioni completate
<lista>

### File modificati
<lista con path>

### Verifica
<come hai validato che funziona — comandi, snapshot, metriche dopo>

---

## Round 2 (YYYY-MM-DD) — ...

---

## Da fare (backlog)

### Alto impatto
- [ ] ...

### Medio impatto
- [ ] ...

### Basso impatto / nice-to-have
- [ ] ...

---

## Snapshot numerico

| Metrica | Prima | Dopo |
|---|---|---|

---

## Note / gotchas

- ...
```

## Rules (memory-enforced)

### Rule 1 — Update CHANGELOG before closing every session

After completing any concrete intervention on any item, **append a dated entry to `work/CHANGELOG.md`** before reporting completion to the user. If multiple items are touched in one session, group them under the same date heading.

### Rule 2 — Open the appropriate `work/<item>.md` at the start of work

When the user mentions an item (site, module, client) by name, the **first read** is `work/<item>.md`. This sets context for everything else in the session. If the file doesn't exist, create it from `templates/work-log-template.md`.

### Rule 3 — Never delete a Round

Rounds are historical records. If a Round 1 fix gets superseded by a Round 4 fix, **both stay in the file**, with cross-reference notes. Future sessions need to know what was tried.

### Rule 4 — Detail in `work/<item>.md`, summary in `CHANGELOG.md`

CHANGELOG is the table of contents. Per-item logs are the chapters. Don't duplicate.

### Rule 5 — Even ad-hoc diagnostics get a CHANGELOG entry

If the user asks "is X a problem?" and you answer "no, it's intentional" — log that. Future sessions need to know the question was already asked and resolved.

### Rule 6 — Mental check before closing

Ask yourself: _"If the user opens a fresh session tomorrow and reads only CHANGELOG.md + the relevant work/<item>.md, do they know what state we're in?"_ If no, fix it now.

## Companion memory file

For the CHANGELOG discipline to survive across Claude sessions, save a memory entry on first use:

```markdown
---
name: Always update CHANGELOG.md after significant work
description: After every concrete intervention on any tracked item, append a dated entry to <project-root>/work/CHANGELOG.md before reporting completion
type: feedback
---
```

Memory location: `~/.claude/projects/<project-slug>/memory/feedback_changelog_discipline.md` + indice in `MEMORY.md`.

## Templates included

In `templates/`:
- `work-log-template.md` — boilerplate per ogni nuovo item (Round 1 placeholder)
- `changelog-entry-template.md` — esempi di voci CHANGELOG (intervento maggiore, incrementale, non-actionable, multi-item, setup nuovo item)

In `templates/examples/` — applicazioni reali del sistema in domini specifici:
- `seo-example/` — esempio di uso per gestire SEO su più siti (work log + CHANGELOG + schema markup snippet + IndexNow pattern). NON parte del core del sistema — è solo un esempio di come si applica.

## Tips per usare bene il sistema

### Naming items
- File: `work/<short-id>.md`. Slug breve, lowercase, no spazi.
- Esempi: `work/customer-rossi.md`, `work/api-gateway.md`, `work/siteA.md`, `work/migration-v2.md`.

### Granularità dei Round
- Un Round = un'unità di lavoro coerente (un set di fix correlati, un deploy, una sessione di refactoring).
- Non un Round per ogni commit. Non un Round per ogni mese.
- **Una sessione di lavoro = uno o più Round**. Multi-day work può estendere lo stesso Round.

### Cosa NON mettere
- **Credenziali in chiaro** — usa `.env` file references, mai inline.
- **Stack traces / log dumps lunghi** — link al file gist o salvali in `work/<item>/<log>.txt` con riferimento dal Round.
- **Conversazione tra te e Claude** — la sostanza, non il dialogo.

### Quando aggiornare
- ✅ Fine sessione, prima del summary all'utente.
- ✅ Cambio di rotta significativo a metà sessione (es. rollback di Round 3, apertura Round 4 con nuovo approccio).
- ❌ Dopo ogni micro-step. Crea noise.
- ❌ Per pure conversational queries senza azione.

## Esempio applicato (multi-progetto reale)

Setup: 5 progetti tracciati in `<root>/work/`. Stato dopo 30 giorni di uso:

```
work/
├── CHANGELOG.md                  ← 10 KB, 30+ giorni di interventi indicizzati
├── itemA.md                      ← Round 1-5
├── itemB.md                      ← Round 1-5
├── itemC.md                      ← Round 1-2
├── itemD.md                      ← Round 1-3
└── itemE.md                      ← Round 1
```

Quando in una nuova sessione l'utente dice _"come stiamo andando su itemB?"_, Claude:
1. Apre `work/CHANGELOG.md` (cerca le voci recenti di itemB)
2. Apre `work/itemB.md` (legge Round più recenti)
3. Risponde con full context — senza che l'utente debba ricostruire nulla.

## Public repo

`github.com/Bunz80/claude-project-rounds` — install globale via:
```bash
git clone https://github.com/Bunz80/claude-project-rounds.git ~/.claude/skills/project-rounds
```

License MIT. PR welcome se trovi pattern aggiuntivi (es. team-shared CHANGELOG, GitHub Actions integration, anti-patterns di dominio specifico, etc.).
