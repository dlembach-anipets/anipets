# Anipets – Produktbeschreibung

Stand: 05.11.2026

## Vision

Anipets (anipets.de) ist ein Preisvergleich für Tierbedarf in Deutschland. Tierhalter von Hunden, Katzen und Pferden sehen auf einen Blick, wo ein Produkt am günstigsten ist. Der Betrieb finanziert sich über Affiliate-Links (Awin, Amazon PartnerNet). Idealo ist das Vorbild für Aufbau und Qualitätsanspruch.

Zielgruppe: Tierhalter, die Verbrauchsprodukte wie Futter regelmäßig kaufen und dabei nicht zu viel zahlen wollen.

Was Anipets von einer Linkliste unterscheidet:

- aktuelle Preise je Shop, mit sichtbarer Aktualität
- Preisverlauf und Preis-Alerts per E-Mail
- ein KI-Assistent (Claude), der Produkte empfiehlt
- Ratgeber zu Futter und Zubehör, die organischen Traffic bringen

## Sprachen

Katalog, Inhalte und Oberfläche sind dreisprachig: Deutsch, Englisch, Französisch. Hauptmarkt bleibt Deutschland. Routen, Slugs und Seiten tragen alle drei Sprachen.

## Funktionen und Phasen

| Funktion | Phase | Inhalt |
| --- | --- | --- |
| Kategorien und Suche | 1 | Hunde, Katzen, Pferde; Volltextsuche mit Tag-Filtern |
| Produktseite | 1 | Shop-Preise im Vergleich, Affiliate-Weiterleitung |
| Design-System | 1 | Warme Off-White/Beige-Basis, DM Serif Display und DM Sans, Akzentfarben je Kategorie (Orange Hunde, Lila Katzen, Grün Pferde), Dark Mode |
| Preis-Pipeline | 2 | Crawling, KI-Extraktion, Review, Veröffentlichung |
| Admin-Bereich für die Review-Queue | 2 | Auffällige Preise prüfen und freigeben |
| Preisverlauf und Aktualitätsanzeige | 2 | Verlauf je Produkt und Shop; Alter des Preises sichtbar |
| SEO-Ausbau | 3 | Schema.org für Produktseiten, Startseite und Ratgeber, Sitemap |
| Preis-Alerts | 3 | E-Mail bei Preisziel, Bestätigung, DSGVO-konforme Abmeldung |
| KI-Chat | 4 | Produktempfehlungen mit Produktkarten |
| Ratgeber/Blog | 4 | Guides zu Futter und Zubehör |

Der MVP ist erreicht, wenn Phase 2 abgenommen ist.

Bewusst nicht im Umfang: Artikelgenerierung per KI und Sales-Monitor (vorerst gestrichen).

## Monetarisierung

Jeder Klick auf einen Shop läuft über eine Weiterleitung von Anipets (`/go`) mit Partner-Tracking und wird gezählt. Affiliate-IDs liegen als Konfiguration im Backend.

## Messgrößen

Zielwerte sind bewusst noch offen.

- ausgehende Klicks pro Woche (Klicks auf Buttons, die zum Shop abspringen)
- Besuche pro Woche nach Kanal (organisch, Social Media, weitere), erfasst über Referrer und UTM-Parameter
- Anteil der Produkte mit einem Preis jünger als N Tage
- Anteil der Preise, die in der Review-Queue landen

## Offen

- Amazon-Preise: Wie kommen sie ins System (Partner-Schnittstelle oder nur Link ohne Live-Preis)? Scraping von Amazon ist wegen der Nutzungsbedingungen heikel.
- Impressum: Daten zu Anipets werden vom Inhaber vor dem Livegang eingetragen.
