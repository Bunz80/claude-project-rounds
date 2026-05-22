# <SITE NAME> — SEO Optimization Log

**Sito:** https://example.it
**GSC property:** `https://example.it/` (siteOwner)
**Stack:** <WordPress + Polylang + Yoast | Laravel 12 | Vite SPA | ...>
**Hosting:** <OVH shared SFTP-only | Plesk SSH + WP-CLI | git push deploy | ...>
**Credenziali:** `/path/to/.env`
**Business:** <1 paragrafo: cosa fa, indirizzo se locale, target>

---

## Diagnosi iniziale (YYYY-MM-DD)

GSC 90 giorni: **N click, N impressions, CTR X%, posizione media Y**.

### Top query (pos 10-25 — già ranking decente)
| Query | Imp | Pos |
|---|---|---|
| ... | ... | ... |

### Query deboli (pos 50+)
- ...

### Top pagine
- `/`: ... imp, CTR ...
- ...

### Problemi tecnici rilevati
1.
2.
3.

---

## Round 1 (YYYY-MM-DD) — <titolo>

### Driver
<perché è stato fatto questo round>

### Operazioni completate

#### a) ...
#### b) ...

### File modificati sul server

```
/path/to/file        ✏️ ...
/path/to/new-file    🆕 ...
```

DB:
- `wp_options.xxx` → ...
- `wp_postmeta` → ...

### Verifica live

| URL | Atteso | Risultato |
|---|---|---|
| ... | ... | ✅ |

---

## Round 2 ...

---

## Da fare (backlog SEO)

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
| ... | ... | ... |

---

## Note / gotchas

-
-

---

## Comandi di lavoro utili

```bash
# Tooling wrapper
/tmp/<prefix>_get.sh ...
/tmp/<prefix>_put.sh ...

# Smoke checks
curl -sI https://example.it/
```
