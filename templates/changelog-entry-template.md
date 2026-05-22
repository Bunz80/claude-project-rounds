# CHANGELOG.md — formato voci

Sezione per data (più recente in alto). Una voce per intervento. Detail link al `work/<item>.md`.

## Tipi di voce

### Intervento maggiore (nuovo Round)

```markdown
## 2026-05-22

- **itemA** v1.2.0 → **v1.4.0**: <1 riga di sintesi>. Vedi `itemA.md` §Round 5.
```

### Intervento incrementale (non un Round nuovo, ma vale la pena loggarlo)

```markdown
## 2026-05-12

- **itemA** — re-submit configurazione dopo cambio
- **itemB** — flush cache dopo deploy
```

### Diagnostica non-actionable (importante: per non ripetere il lavoro)

```markdown
## 2026-05-21

- **itemB** — diagnosticato alert "X" → **non actionable**. <Motivo in 1 riga>. Vedi `itemB.md` §Note.
```

### Multi-item nello stesso giorno

```markdown
## 2026-05-11

**Big push su 4 item.**

- **itemA** v1.2 → **v1.4**: <sintesi>. Vedi `itemA.md` §Round 5.
- **itemB** v1.1 → **v1.3**: <sintesi>. Vedi `itemB.md` §Round 4-5.
- **itemC** (commit `d15baac`): <sintesi>. Vedi `itemC.md` §Round 2.
- **itemD** v1.2 → **v1.3**: <sintesi>. Vedi `itemD.md` §Round 3.
```

### Setup nuovo item

```markdown
## 2026-05-12

- **itemE (NEW)** — Round 1 setup completo:
  - <bullet 1>
  - <bullet 2>
  - <bullet 3>
  - Vedi `itemE.md`
```

### Decisioni di processo (skill, memory, convention)

```markdown
## 2026-05-22

- **CHANGELOG inizializzato** — questo file. Memory rule attiva: aggiornare dopo ogni intervento.
- **Skill `<name>` creata** in `~/.claude/skills/<name>/`. Repo pubblico: <url>.
```

## Best practices

- **Mai più di 1 riga per item** in CHANGELOG. Il dettaglio sta nel `work/<item>.md`.
- **Sempre cross-reference** al Round / sezione corrispondente nel per-item log.
- **Includere versioni** quando applicabile (`v1.2.0 → v1.4.0`) — aiuta a capire la cadenza dei cambi.
- **Stato `non actionable`** sempre marcato come tale, così future sessions non rifanno il lavoro.
