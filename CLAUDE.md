# Anipets

Anipets (anipets.de) ist ein Preisvergleich für Tierbedarf (Hunde, Katzen, Pferde) in Deutschland, finanziert über Affiliate-Links (Awin, Amazon PartnerNet). Vorbild ist Idealo. Dies ist der Neustart ohne Lovable und ohne n8n.

Produkt und Architektur stehen in `docs/PRODUCT.md` und `docs/ARCHITECTURE.md`. Lies beide, bevor du etwas Größeres änderst.

## Stack

- Frontend: Astro mit Vorab-Rendering, React-Inseln für Chat und Filter, in `apps/web/`
- Hosting: Cloudflare (Workers oder Pages)
- Pipeline: Cron-Worker in `workers/pipeline/`
- Datenbank und Backend: bestehendes Supabase-Projekt (Postgres, RLS, Edge Functions), Migrationen und Funktionen in `supabase/`
- Crawling: Firecrawl. KI: Claude, hinter einer eigenen Schnittstelle. E-Mail: Resend.
- CI: GitHub Actions deployt nach Cloudflare und spielt Migrationen in Supabase ein

## Befehle

Trage die Befehle hier ein, sobald sie in Phase 0 existieren: Entwicklung starten, bauen, testen, deployen, Migration anlegen und einspielen.

## Regeln

- Schemaänderungen nur als Migration in `supabase/migrations/`, nie direkt in der Datenbank.
- Jede neue Tabelle bekommt RLS und passende Policies.
- Preise schreibt nur die Pipeline, nie das Frontend.
- Schlüssel und Secrets nie ins Repo; sie liegen in Cloudflare und Supabase. Im Frontend nur der öffentliche Supabase-Schlüssel.
- Katalog, Inhalte und Oberfläche sind dreisprachig (DE, EN, FR). Neue Texte kommen in allen drei Sprachen; Deutsch ist die Leitsprache.
- Ein Lauf der Pipeline ohne Arbeit endet sofort und ruft keinen externen Dienst auf.
- Schwellwerte der Pipeline (Alter, Preisabweichung, Konfidenz) sind Konfiguration, kein fester Code.
- Arbeite in kleinen Schritten, immer entlang der aktuellen Phase. Eine Phase gilt erst als fertig, wenn ihre Abnahmekriterien aus `docs/ARCHITECTURE.md` erfüllt sind.
- Nicht im Umfang: Artikelgenerierung per KI und Sales-Monitor.

## Aktuelle Phase

Phase 0: Fundament. Ziel und Abnahme stehen in der Roadmap in `docs/ARCHITECTURE.md`.

## Design

Warme Off-White/Beige-Basis (60-30-10), DM Serif Display für Überschriften, DM Sans für Text, Akzentfarben je Kategorie (Orange Hunde, Lila Katzen, Grün Pferde), Dark Mode, dezente Bewegung. Nicht verwenden: experimentelle Navigation, Neo-Brutalism, Maximalism.
