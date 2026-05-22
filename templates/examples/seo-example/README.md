# SEO example — applicazione di project-rounds al SEO multi-sito

Esempio reale di come il sistema `project-rounds` è stato applicato per gestire SEO su 5 siti in parallelo.

**Setup:**
- Project root: `<somewhere>/SEO/`
- Items tracciati: `siteA`, `siteB`, `siteC`, `siteD`, `siteE`
- 5 stack diversi (WordPress + Yoast/Rank Math, Laravel, Vite SPA, statico, WP + WooCommerce)
- Trigger primario di Round: alert GSC (Google Search Console) via email

## File di questo esempio

- `local-business-schema.json` — schema completo `LocalBusiness/Store` con address, geo, openingHours, areaServed, makesOffer, contactPoint. Pronto da incollare in JSON-LD.
- `faqpage-schema.json` — pattern minimale `FAQPage` con `mainEntity[]` di `Question/Answer`.

## Round / CHANGELOG specifici al SEO

Cosa **tipicamente** finisce in un Round SEO:
- Fix Yoast / Rank Math meta (title, description, OG)
- Schema markup (LocalBusiness, FAQPage, Merchant return + shipping, AggregateOffer)
- Sitemap submit / re-submit
- IndexNow setup + batch ping
- Slug rename + 301 redirect
- Polylang / WPML hreflang fixes
- robots.txt updates
- Canonical URL fixes

Cosa **tipicamente** finisce in CHANGELOG:
- Una riga per intervento, cross-link al Round.
- Alert GSC ricevuti + classificazione (actionable / non-actionable).
- Setup di nuovi siti tracciati.

## Anti-pattern documentati nei work log SEO reali

Esempi di gotcha che vale la pena loggare:
- **Polylang Free `-it` slug regression** — abilitare Polylang su una taxonomy esistente a volte rinomina gli slug aggiungendo `-{lang}` → 404 sulle URL pubbliche.
- **Blade 12 `@context` directive conflict** (Laravel) — `@context` parsato come direttiva, JSON-LD malformato. Fix: blocco `@php ... @endphp`.
- **Canonical loop `/en/` → `/en/home-2/`** — WP + Polylang dichiari canonical `/en/` ma WP redirige a `/en/home-2/`. Fix: filter `redirect_canonical` che ritorna `false` per `/en/`.
- **OG title duplicato** — Yoast + theme custom emettono entrambi `og:title`. Risultato: 2 tag og:title. Fix: rimuovere quello del theme.
- **WP-CLI Plesk default PHP 5.4** — usare path esplicito: `/opt/plesk/php/8.1/bin/php /usr/local/bin/wp ...`.

Questi sono **esempi del valore del sistema**: vengono loggati una volta nel work log e ritrovati istantaneamente quando il problema si ripresenta in altri progetti.

## Da non confondere

Questo esempio NON fa parte del core di `project-rounds`. È solo una dimostrazione di come il sistema si applica a un dominio specifico (SEO). Il core resta domain-agnostic.

Per scenari diversi (deploy ops, content production, client work), il pattern è identico, cambia solo cosa va dentro i Round.
