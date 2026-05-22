# Formato voci CHANGELOG.md

## YYYY-MM-DD

- **<site>** <tipo intervento>: <1-line summary>. Vedi `<site>.md` §Round N.

## Esempi

### Intervento maggiore (Round nuovo)

## 2026-05-11

- **acnoleggio** mu-plugin v1.2.0 → **v1.4.0**: FAQ home + /noleggio/, IndexNow auto-submit, custom image sitemap (57 immagini). Vedi `acnoleggioischia.md` §Round 5.

### Intervento incrementale (non un Round)

## 2026-05-12

- **palamaro** — IndexNow batch 6 URL → HTTP 202

### Diagnostica non-actionable (importante: serve per non rifare il lavoro)

## 2026-05-21

- **mazzella** — alert GSC "Bloccata da robots.txt" diagnosticato → **non actionable**. Tutte le URL bloccate sono volute. Vedi `mazzellarent.md`.

### Multi-site nello stesso giorno

## 2026-05-11

**Round 4-5 di interventi autonomi su tutti i siti WP.**

- **acnoleggio** mu-plugin v1.2.0 → **v1.4.0**: ...
- **mazzella** mu-plugin v1.1.0 → **v1.3.0**: ...
- **palamaro** (commit `d15baac`): ...
- **ottica** mu-plugin v1.2.0 → **v1.3.0**: ...

### Setup nuovo sito

## 2026-05-12

- **myworkplan.it (NEW)** — Round 1 setup SEO completo dal nulla:
  - Credenziali registrate
  - `index.html` riscritto con meta + Schema
  - Creati `/robots.txt`, `/sitemap.xml`, `/llms.txt`
  - IndexNow key attiva
  - GSC sitemap submitted → pending
  - Vedi `myworkplan.md`
