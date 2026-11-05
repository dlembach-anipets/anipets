# Anipets – Architektur

Stand: 05.11.2026

## Überblick

Postgres in Supabase ist die einzige Quelle der Wahrheit. Das Frontend liest, die Pipeline schreibt, Edge Functions bedienen Alerts und Chat. Ein GitHub-Repo mit CI liefert Code nach Cloudflare und Migrationen nach Supabase. Lovable und n8n entfallen.

```
Besucher ──> Cloudflare: Astro-Frontend ──liest──> Supabase: Postgres (RLS)
                         Pipeline-Worker (Cron) ──schreibt──> Postgres
                              │        │
                          Firecrawl   Claude
             Supabase: Edge Functions (Alerts, Abmeldung, KI-Chat) ──> Postgres, Claude, Resend
GitHub (Code, CI, Migrationen) ──deployt──> Cloudflare, Supabase
```

## Stack

| Baustein | Wahl | Hinweis |
| --- | --- | --- |
| Frontend | Astro, Vorab-Rendering, React-Inseln für Chat und Filter | SEO und Ladezeit; i18n-Routing für DE, EN, FR |
| Hosting | Cloudflare (Workers oder Pages, aktuelle Empfehlung beim Setup prüfen) | Cron-Trigger für die Pipeline |
| Datenbank, Backend | Bestehendes Supabase-Projekt (Postgres, RLS, Edge Functions), Region Frankfurt | Schema wird als Baseline-Migration ins Repo gezogen |
| Automatisierung | Cron-Worker und Edge Functions im Repo | ersetzt n8n |
| Crawling | Firecrawl | |
| KI | Claude für Preis-Extraktion und Chat, hinter eigener Schnittstelle | ersetzt GPT-4o-mini |
| E-Mail | Resend | Alert-Mails, Bestätigungen |
| Affiliate | Awin und Amazon PartnerNet | Tracking-IDs als Konfiguration |
| Code, CI | GitHub, CI deployt nach Cloudflare und spielt Migrationen in Supabase ein | |
| Entwicklung | Claude Code | |

## Datenmodell (Bestand, 33 Tabellen in `public`, RLS überall aktiv)

- **Katalog:** `products`, `product_variants`, `product_specifications`, `finder_tags`, `pet_categories`, `url_mappings`
- **Preise:** `store_prices`, `variant_prices`, `price_history`, `price_review_queue`, `product_import_drafts`
- **Shops und Affiliate:** `stores`, `store_affiliate_configs`, `store_scraping_config`, `store_discount_codes`
- **Nutzer:** `profiles`, `user_roles`, `price_alerts`, `wishlists`
- **Inhalte:** `blog_posts`, `blog_categories`, `blog_tags`, `blog_post_tags`, `blog_post_products`, `campaigns`, `campaign_products`, `sponsored_deals`, `detected_sales`
- **Betrieb:** `site_settings`, `imprint_settings`, `social_links`, `ip_blocklist`, `rate_limit_offenses`

Bekannte Schwächen, die der Neustart behebt:

- `store_prices.store` ist Text statt Verweis auf `stores`; `price_history` kennt nur das Produkt, nicht den Shop. Vorschlag: Fremdschlüssel auf `stores`, Verlauf je Produkt und Shop.
- Es gibt keine Tabelle für Klicks auf Affiliate-Links. Neu: `click_events` mit Produkt, Shop, Zeitpunkt, Referrer und UTM-Parametern.
- `imprint_settings` enthält Platzhalterdaten und wird vor dem Livegang befüllt.
- Katalog und Inhalte sind dreisprachig (DE, EN, FR) und bleiben es.
- Die Schalter `cron_article_generation_enabled` und `cron_sales_monitor_enabled` in `site_settings` bleiben vorerst ungenutzt.

## Preis-Pipeline

Läuft als Cron-Worker auf Cloudflare:

1. **Auswahl:** Einträge aus `store_prices`, deren Preis älter als ein Schwellwert ist. Ist nichts zu tun, endet der Lauf sofort (kein leerer Aufruf an Firecrawl).
2. **Abruf:** Firecrawl lädt die Shop-Seite, bei Bedarf mit JavaScript-Rendering (`store_scraping_config`).
3. **Extraktion:** Claude liest Preis, Versand und Verfügbarkeit und liefert strukturierte Werte mit Konfidenz.
4. **Prüfung:** Starke Abweichung oder niedrige Konfidenz führt in `price_review_queue` statt in `store_prices`.
5. **Schreiben:** Unauffällige Preise aktualisieren `store_prices` und hängen einen Eintrag an `price_history` an.
6. **Review:** Der Admin-Bereich zeigt die Queue; Einträge werden freigegeben oder verworfen.

Betrieb: Jeder Lauf schreibt ein Protokoll (geprüft, geschrieben, in Review, Fehler); Fehler lösen eine Benachrichtigung aus.

Startwerte als Konfiguration, später änderbar: Preis älter als 24 Stunden, Abweichung über 25 Prozent, Konfidenz unter 0,8.

Amazon-Preise sind ungeklärt (siehe `PRODUCT.md`) und laufen nicht automatisch durch diese Pipeline.

## Sicherheit, DSGVO, SEO

- RLS auf allen Tabellen; Rollen über `user_roles` (admin, moderator, user). Die Pipeline schreibt nur mit dem Service-Schlüssel im Worker, nie im Frontend.
- Alerts: Limit von 10 pro Nutzer per Trigger, E-Mail-Bestätigung, Abmeldung über Edge Function mit `unsubscribe_token`.
- Missbrauchsschutz: `ip_blocklist`, `rate_limit_offenses`, zusätzlich Cloudflare-Limits.
- Secrets (Firecrawl, Claude, Resend, Awin, Amazon) liegen in Cloudflare und Supabase, nie im Repo.
- Rechtstexte: Impressum, Datenschutzerklärung, Hinweis auf Affiliate-Links, Preisangaben mit Stand und Versandhinweis; juristisch prüfen lassen.
- SEO: Vorab-gerenderte Produkt-, Kategorie- und Ratgeberseiten, Schema.org (Product, Offer), Sitemap, Slugs je Sprache, sichtbare Preis-Aktualität.

## Ablösung

| Bisher | Neu | Phase |
| --- | --- | --- |
| Lovable-Frontend | Astro auf Cloudflare | 1 |
| n8n Re-Crawling | Pipeline-Worker | 2 |
| Review-Queue-Admin (nie gebaut) | Admin-Bereich in Astro hinter Login | 2 |
| n8n Alert-Mails | Edge Function plus Resend | 3 |
| n8n KI-Chat | Edge Function mit festem Ausgabeschema für Produktkarten | 4 |

n8n läuft, bis die jeweilige Ersatzfunktion live ist, und wird dann je Workflow abgeschaltet.

## Roadmap

| Phase | Inhalt | Abnahme |
| --- | --- | --- |
| 0 Fundament | Repo, Cloudflare-Deploy aus `main`, Preview-Deploys, Baseline-Migration, Bestandsaufnahme der Daten, `CLAUDE.md` | Leere Astro-Seite läuft auf Cloudflare; Schema baut sich lokal aus den Migrationen auf; Supabase-Advisors ohne kritische Funde |
| 1 Katalog und Vergleich | Startseite, Kategorien, Suche, Produktseite mit Shop-Preisen, `/go` mit Klickprotokoll, Design-System, Mehrsprachigkeit (DE, EN, FR), Impressum | Ein Produkt ist von der Startseite bis zum Shop-Klick durchklickbar; Klick steht in `click_events`; Seitenquelltext enthält Produktname und Preis |
| 2 Preis-Pipeline | Cron-Worker, Review-Queue, Admin-Bereich, Preisverlauf, Aktualitätsanzeige, Lauf-Protokoll | Läufe der letzten Woche fehlerfrei im Protokoll; auffällige Preise in der Queue; Verlauf sichtbar; n8n-Re-Crawling abgeschaltet |
| 3 SEO und Alerts | Schema.org, Sitemap, Preis-Alerts mit Bestätigung und Abmeldung | Strukturierte Daten bestehen den Google-Test für Rich Results; Alert-Mail kommt an, Abmeldung funktioniert; n8n-Alert-Workflow abgeschaltet |
| 4 KI-Chat und Ratgeber | Chat mit Produktkarten, Ratgeberseiten | Chat liefert Produktkarten im festen Schema; Ratgeber vorab gerendert; n8n komplett abgeschaltet |
