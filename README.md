# seo-rounds

Round-based multi-site SEO workflow skill for [Claude Code](https://claude.com/claude-code).

A reusable system to manage SEO across multiple websites with full traceability, session-loss resilience, and ready-to-use adapters for the 5 most common stacks (WordPress + Yoast / Rank Math, Laravel, Vite SPA, static).

## What it does

- **Per-site work logs** (`work/<site>.md`) with numbered Rounds
- **Central CHANGELOG.md** as cross-site index (reverse-chrono)
- **Multi-stack access patterns** — SFTP-only OVH, SSH+WP-CLI Plesk, Laravel git push, FTP-only Vite, FTP+DB direct
- **Schema.org snippet library** — LocalBusiness, FAQPage, MerchantReturnPolicy + OfferShippingDetails, AggregateOffer
- **IndexNow setup** — key generation, /key.txt route patterns (WP + Laravel), auto-submit on save_post
- **GSC alert diagnosis** — canonical duplicate, noindex, robots blocked, 404, merchant listings — root cause + standard fix
- **Anti-patterns** — Polylang `-it` slug regression, Blade 12 `@context` conflict, canonical loop `/en/home-2/`, OG title duplicato, etc.

## Install

### Global (all projects on your machine)

```bash
git clone https://github.com/<user>/claude-seo-rounds.git ~/.claude/skills/seo-rounds
```

The skill becomes available in any Claude Code session.

### Project-specific

```bash
git clone https://github.com/<user>/claude-seo-rounds.git .claude/skills/seo-rounds
```

## Triggers

The skill activates automatically when the user says:
- "SEO round", "audit SEO", "verifica SEO"
- "GSC alert", "Search Console", "alert robots", "alert noindex"
- "canonical loop", "duplicate canonical"
- "Polylang slug", "WPML hreflang"
- "schema markup", "merchant return policy", "shippingDetails"
- "IndexNow", "submit sitemap"
- "indicizzazione lenta", "Discovered currently not indexed"
- "work log SEO", "CHANGELOG SEO"

## Project structure expected

```
<project-root>/
├── work/
│   ├── CHANGELOG.md
│   ├── <site1>.md
│   └── <site2>.md
└── <site1>/.env
```

## Templates included

- `templates/work-log-template.md` — boilerplate for `work/<site>.md`
- `templates/changelog-entry-template.md` — CHANGELOG entry format examples
- `templates/local-business-schema.json` — full LocalBusiness/Store schema
- `templates/faqpage-schema.json` — FAQPage schema pattern

## License

MIT — use it, fork it, ship it.

## Contributing

Issues and PRs welcome. If you found a new anti-pattern or a stack-specific gotcha, add it to `SKILL.md` under "Anti-patterns documentati".
