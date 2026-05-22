---
name: seo-rounds
description: Round-based multi-site SEO workflow with per-site work logs, central CHANGELOG, GSC integration, and ready-to-use snippets for schema markup, IndexNow, sitemap, Polylang/WPML fixes. Use when managing SEO across multiple websites with different stacks (WordPress + Yoast/Rank Math, Laravel, Vite SPA, static), responding to Google Search Console alerts (canonical, noindex, robots, merchant listings, 404), applying schema markup at scale, or building a persistent SEO process that survives session loss. Triggers su "SEO round", "GSC alert", "work log SEO", "CHANGELOG SEO", "schema markup", "IndexNow", "Polylang slug", "WPML hreflang", "canonical loop", "merchant return policy", "shippingDetails", "Yoast fix", "Rank Math noindex", "audit SEO", "sitemap immagini", "indicizzazione lenta", "duplicate canonical".
---

# SEO Rounds — multi-site workflow

Un sistema riusabile per gestire SEO su 1-N siti contemporaneamente con tracciabilità completa, resilienza alle sessioni perse, e adapter per i 4-5 stack più comuni (WordPress + Yoast, WordPress + Rank Math, Laravel, Vite SPA / statico).

## Quando usare

Attiva questa skill quando:
- L'utente gestisce **più siti** e vuole coordinare SEO cross-site (audit, fix, monitoring)
- Arriva un **alert GSC** (Google Search Console) via email o l'utente lo riporta in chat
- Serve applicare **schema markup**, configurare **IndexNow**, fixare **canonical/noindex/sitemap** issues
- Si lavora su **Polylang/WPML** multilingua e si incontrano slug suffix, canonical loop, hreflang regression
- Serve un **changelog persistente** delle modifiche SEO per non perdere il filo tra sessioni
- L'utente chiede "come stiamo andando" / "verifica" / "che cosa abbiamo fatto"

Non attivare per: domande puramente teoriche su SEO, code review non-SEO, lavoro di sviluppo che non tocca on-page/technical SEO.

## File system convention

Il workflow vive in una **GSC project directory** che ha questa struttura:

```
<project-root>/
├── work/
│   ├── CHANGELOG.md              ← indice cross-site, reverse-chrono
│   ├── <site1>.md                ← work log per sito (Round 1, Round 2, ...)
│   ├── <site2>.md
│   └── ...
├── <site1-name>/.env             ← credenziali, fuori da work/
└── <site2-name>/.env
```

Esempio reale: `/Users/bunz/Desktop/www/GSC/work/` contiene `CHANGELOG.md` + un `.md` per ognuno dei 5 siti gestiti.

## CHANGELOG discipline (memory rule)

**Regola critica:** dopo ogni intervento concreto su qualunque sito, **appendere una voce datata** in `work/CHANGELOG.md` PRIMA di chiudere la sessione/task. Reverse-chrono (più recente in alto), grouped by date.

Esempio voce:
```markdown
## 2026-05-11

- **acnoleggio** mu-plugin v1.2.0 → **v1.4.0**: FAQ home + /noleggio/, IndexNow auto-submit, custom image sitemap (57 immagini). Vedi `acnoleggioischia.md` §Round 5.
- **mazzella** mu-plugin v1.2.0 → **v1.3.0**: FAQ schema home + servizi, CWV preconnect. Vedi `mazzellarent.md` §Round 5.
```

Il CHANGELOG è **indice**, non duplica i dettagli. I dettagli stanno in `work/<site>.md`. Un nuovo Claude session che legge solo CHANGELOG.md deve poter capire lo stato di ogni sito in 30 secondi.

## Round system (per-site work log)

Ogni `work/<site>.md` è un log incrementale a **Round numerati**. Pattern:

```markdown
# <Site Name> — SEO Optimization Log

**Sito:** https://example.it
**GSC property:** `https://example.it/` (siteOwner)
**Stack:** WordPress 6.x + Polylang + Yoast Free
**Hosting:** OVH shared — SFTP-only
**Credenziali:** `/path/to/.env`
**Business:** <1 paragrafo: cosa fa, indirizzo, telefono, target>

---

## Diagnosi iniziale (YYYY-MM-DD)

GSC 90gg: <N click, N imp, CTR%, pos>
Top query: <tabella>
Problemi rilevati:
1. <problema>
2. ...

---

## Round 1 (YYYY-MM-DD) — <titolo>

### Driver
<perché è stato fatto questo round>

### Operazioni completate
<lista dettagliata>

### Verifica live
<tabella check HTTP/schema/canonical>

### File modificati
<lista>

---

## Round 2 (YYYY-MM-DD) — <titolo>
...
```

Vincoli:
- **Mai cancellare Round vecchi** — sono record storico. Se un fix è poi stato sostituito da un altro, mantieni entrambi con nota cross-reference.
- **Backlog separato in coda** al file in 3 sezioni: Alto impatto / Medio impatto / Basso impatto.
- **Snapshot numerico** in coda: tabella prima/dopo (es. "Image alt count: 0 → 524").

## Multi-stack access patterns

Adapter pratici per i 5 stack ricorrenti. Salvali in `/tmp/<prefix>_*.sh` come wrapper.

### 1. WordPress su OVH SFTP-only (no SSH, no WP-CLI remoto)
Pattern: upload PHP script tramite SFTP → invoca via HTTPS con token → auto-elimina.

```bash
# /tmp/<x>_run.sh
LOCAL="$1"
BASE=$(basename "$LOCAL")
sshpass -p "$PASS" sftp ... <<EOF
put $LOCAL www/$BASE
bye
EOF
curl -s "https://site.it/$BASE?token=mzseo2026"
sshpass ... rm www/$BASE
```

Lo script PHP include `require_once __DIR__ . '/wp-load.php'` per bootstrap WP. Token hardcoded come misura light; lo script è sempre temporaneo.

### 2. WordPress su Plesk con SSH + WP-CLI
Wrapper con path PHP esplicito (Plesk default CLI è PHP 5.4):

```bash
# /tmp/<x>_wp.sh
ssh -o LogLevel=QUIET "$USER@$HOST" \
  "/opt/plesk/php/8.1/bin/php /usr/local/bin/wp --path=$DOCROOT $*"
```

Esempio: `/tmp/mz_wp.sh "option get rank-math-options-sitemap"`.

### 3. Laravel con git push deploy (Plesk)
```bash
git push prod main
# Plesk webhook → composer install → restart PHP-FPM (sito 503 per ~20-30s)
# poi 200
```

Attendere il restart con: `until curl -sf -o /dev/null "https://site.it/"; do sleep 3; done`.

### 4. FTP-only Vite / statico (no SSH)
```bash
# /tmp/<x>_put.sh
curl -s -T "$LOCAL" --user "$USER:$PASS" "ftp://$HOST/httpdocs/$REMOTE"
```

Nessun runtime PHP, solo upload file statici.

### 5. WordPress con FTP + DB direct (no SSH ma DB accessibile)
PDO direct connection per query/migrate slug:

```php
$pdo = new PDO("mysql:host=$DB_HOST;dbname=$DB_NAME;charset=utf8mb4", $USER, $PASS);
$pdo->exec("UPDATE wp_posts SET post_name='new-slug' WHERE ID=$id");
$pdo->exec("INSERT INTO wp_postmeta(post_id,meta_key,meta_value) VALUES($id,'_wp_old_slug','old-slug')");
```

## Schema.org snippet library (copy-paste ready)

Pattern testati live, validati Google Rich Results.

### MerchantReturnPolicy + OfferShippingDetails (per ogni Offer / AggregateOffer)
Necessari dal 2026 per qualificarsi al rich result "Merchant listings".

```php
function merchant_return_policy(): array {
    return [
        '@type'                => 'MerchantReturnPolicy',
        'applicableCountry'    => 'IT',
        'returnPolicyCategory' => 'https://schema.org/MerchantReturnFiniteReturnWindow',
        'merchantReturnDays'   => 2,
        'returnMethod'         => 'https://schema.org/ReturnByMail',
        'returnFees'           => 'https://schema.org/FreeReturn',
    ];
}

function offer_shipping_details(): array {
    return [
        '@type'        => 'OfferShippingDetails',
        'shippingRate' => ['@type' => 'MonetaryAmount', 'value' => 0, 'currency' => 'EUR'],
        'shippingDestination' => [
            '@type'          => 'DefinedRegion',
            'addressCountry' => 'IT',
            'addressRegion'  => 'Campania',
        ],
        'deliveryTime' => [
            '@type'        => 'ShippingDeliveryTime',
            'handlingTime' => ['@type' => 'QuantitativeValue', 'minValue' => 0, 'maxValue' => 1, 'unitCode' => 'DAY'],
            'transitTime'  => ['@type' => 'QuantitativeValue', 'minValue' => 0, 'maxValue' => 1, 'unitCode' => 'DAY'],
        ],
    ];
}
```

Mergi questi due array dentro ogni nodo `Offer` o `AggregateOffer` emesso dal sito.

### LocalBusiness completo (per local business / e-commerce con sede)
Vedi `templates/local-business-schema.json`.

### FAQPage (homepage, condizioni, FAQ pagine dedicate)
Vedi `templates/faqpage-schema.json`.

## IndexNow setup standard

Step minimi (5 min):

1. Generate key: `openssl rand -hex 16`
2. Upload `<key>.txt` (content === key) a `/httpdocs/` (sito root)
3. Save key in WP option o equivalente: `update_option('xxx_indexnow_key', $key)`
4. Submit batch URL:

```bash
curl -X POST "https://api.indexnow.org/IndexNow" \
  -H "Content-Type: application/json" \
  -d '{
    "host": "site.it",
    "key": "<KEY>",
    "keyLocation": "https://site.it/<KEY>.txt",
    "urlList": ["https://site.it/", "https://site.it/page1/"]
  }'
```

Risposta 200/202 = accepted. Bing/Yandex/etc.

### Auto-submit hook on save_post (WordPress)

```php
add_action('transition_post_status', function($new, $old, $post) {
    if ($new !== 'publish') return;
    if (wp_is_post_revision($post) || wp_is_post_autosave($post)) return;
    $key = get_option('xxx_indexnow_key');
    if (!$key) return;
    $url = get_permalink($post);
    $guard = 'xxx_in_' . md5($url);
    if (get_transient($guard)) return;
    set_transient($guard, 1, MINUTE_IN_SECONDS * 5);
    wp_remote_post('https://api.indexnow.org/IndexNow', [
        'headers' => ['Content-Type' => 'application/json'],
        'body'    => wp_json_encode([
            'host' => parse_url(home_url(), PHP_URL_HOST),
            'key' => $key,
            'keyLocation' => home_url("/$key.txt"),
            'urlList' => [$url],
        ]),
        'blocking' => false,
    ]);
}, 20, 3);
```

### Laravel IndexNow `/key.txt` route (no env dependency)
Funziona anche se non hai accesso a settare env var su hosting Plesk:

```php
Route::get('/{key}.txt', function (string $key) {
    return response($key)
        ->header('Content-Type', 'text/plain')
        ->header('Cache-Control', 'public, max-age=2592000');
})->where('key', '[a-f0-9]{32}');
```

## Common GSC alerts → diagnosi + fix

### "Pagina duplicata, Google ha scelto canonica diversa"

**Cause comuni:**
1. **Canonical loop**: dichiari canonical `/en/` ma `/en/` redirige a `/en/home-2/`. Google segue redirect e sceglie target.
   - Fix: rimuovi redirect server-side OR cambia canonical
   - WordPress specifico: `remove_filter('template_redirect', 'redirect_canonical')` per URL specifici
2. **Cache GSC stale**: Google ha indicizzato URL legacy prima del 301. Aspetta re-crawl o usa IndexNow per accelerare.
3. **WPML/Polylang slug**: page tradotta ha slug `home-2`, va rinominato o canonical va forzato.

### "Esclusa noindex" — quasi sempre informativo

Quando vedi questo alert, **prima** verifica se le pagine listed sono INTENZIONALMENTE noindex:
- `/cart/`, `/checkout/`, `/my-account/`
- `/page/N/` paginazioni
- `/?s=` search
- `/author/` archives
- `/category/uncategorized/`

Se sì → ignore, alert è informativo. Google ti sta dicendo "non sto indicizzando queste, è voluto?". La risposta è sì.

Se NO (pagina importante con noindex) → check `wpseo_titles` opzioni o filter Rank Math + `_yoast_wpseo_meta-robots-noindex` postmeta.

### "Non trovata (404)"

Per WordPress + Polylang/WPML: check se la tassonomia o pagina ha slug con suffisso `-it` o `-en`. Polylang in versione Free a volte rinomina slugs in `*-{lang}` quando abiliti multilingua su un CPT/taxonomy.

Fix:
```php
$term = get_term_by('slug', 'foo-it', 'rental_category');
wp_update_term($term->term_id, 'rental_category', ['slug' => 'foo']);
flush_rewrite_rules(false);
```

### "Bloccata da robots.txt" — quasi sempre informativo

Stesso pattern dell'alert noindex: Google ti dice "ho visto link a queste URL ma robots me le blocca". Se sono `/cart/`, `?add-to-cart=`, `?orderby=`, etc. → è voluto.

Check critico: nessuna URL **utile** deve essere disallow. Verifica con curl + grep su robots.txt e disallow non sovra-permissivi.

## Anti-patterns documentati (per non rifarli)

### 1. Polylang Free `-it` slug regression
Quando abiliti Polylang su un CPT/taxonomy esistente, **a volte** Polylang rinomina gli slug aggiungendo `-{lang}`. Risultato: URL pubbliche rompono (404), Yoast taxonomy meta perde collegamento. Fix: rinominare slug back via `wp_update_term`.

### 2. Blade 12 `@context` directive conflict (Laravel)
Blade 12 parsa `@context` e `@graph` come direttive PHP (Laravel Context). JSON-LD inline esce malformato. Fix: costruire JSON-LD in blocco `@php ... @endphp` e poi `{!! json_encode($graph) !!}`. **Mai** mettere `'@context' => ...` come array literal in `{!! json_encode([...]) !!}`.

### 3. Canonical loop `/en/` → `/en/home-2/`
WordPress + Polylang Free senza "URL modifications" → la home tradotta mantiene slug `home-2`. Se dichiari canonical `/en/` ma WP redirige a `/en/home-2/`, hai un loop. Fix:
```php
add_filter('redirect_canonical', function($redirect, $requested) {
    if (rtrim(parse_url($requested, PHP_URL_PATH) ?? '', '/') === '/en') {
        return false;
    }
    return $redirect;
}, 10, 2);
```

### 4. OG title duplicato (Yoast + theme)
Alcuni themes emettono propri `<meta property="og:title">` che convive con quello di Yoast. Risultato: 2 tag og:title in HTML, social scraper inconsistenti. Fix: rimuovere quello del theme o disabilitare Yoast OG specifico.

### 5. WP-CLI Plesk default PHP 5.4
Plesk hosting CLI default è PHP 5.4. WP-CLI moderno richiede ≥7.4. Usa path esplicito a PHP 8.x: `/opt/plesk/php/8.1/bin/php /usr/local/bin/wp ...`.

### 6. `redirect_canonical` per author archive WPML
`is_author()` di WP non riconosce path WPML `/en/author/*` e `/de/author/*`. Per applicare noindex devi check `$_SERVER['REQUEST_URI']` con regex `^/(en|de)/author/`.

## Standard pre-deployment checklist

Prima di chiudere un Round, verifica:

- [ ] Lint codice (`php -l`, `npm run build`)
- [ ] Backup file modificato server-side (`cp x.php x.php.bak-$(date +%Y%m%d%H%M)`)
- [ ] Upload via wrapper appropriato
- [ ] Cache-busting verify: `curl -s "https://site.it/?cb=$(date +%s)"`
- [ ] HTTP 200 check
- [ ] Specific element check (meta tag, schema node, redirect)
- [ ] Rich Results Test (manuale: search.google.com/test/rich-results)
- [ ] Submit sitemap se ha cambiato struttura URL
- [ ] IndexNow ping per URL toccate
- [ ] CHANGELOG.md updated
- [ ] work/<site>.md Round X documentato

## Templates inclusi

Vedi `templates/` per snippet pronti:
- `work-log-template.md` — scheletro work/<site>.md
- `changelog-entry-template.md` — formato voce CHANGELOG
- `local-business-schema.json` — schema completo per businesses locali
- `faqpage-schema.json` — FAQ schema pattern

## Esempio invocazione

User dice: "ho ricevuto un alert GSC su acnoleggio per canonical duplicato"

Risposta agente:
1. Apri `work/acnoleggioischia.md` per stato attuale
2. Apri `work/CHANGELOG.md` per voci recenti acno
3. Esegui `mcp__gsc__inspect_url_enhanced` su URL sospette
4. Diagnosi: confronto `google_canonical` vs `user_canonical`
5. Fix nel mu-plugin via tooling wrapper
6. Verify live con curl
7. **Append entry a CHANGELOG.md**
8. **Aggiungi Round N+1 a work/acnoleggioischia.md**
9. Report sintesi all'utente

## Note progettuali

- La skill è **stack-agnostic**: i pattern WordPress sono in netta maggioranza ma le sezioni Laravel/Vite/statico sono parallele. Adatta in base al progetto corrente.
- Il **Round system** è inspired by changelog incrementali tipo Keep a Changelog ma applicato a SEO operations.
- L'utente è italiano per default → rispondi in italiano quando il contesto è italiano. CHANGELOG può rimanere bilingue (titoli IT, comandi EN).
- **Mai** committare credenziali nel CHANGELOG o nei work logs. Mantenere `.env` esterni a `work/`.

## Repository pubblico

Questa skill è progettata per essere pubblicata su GitHub come template per altri SEO/dev che usano Claude Code. Il path canonico è:
- Locale: `~/.claude/skills/seo-rounds/`
- Pubblico: `github.com/<user>/claude-seo-rounds` (sym-link o git clone)
