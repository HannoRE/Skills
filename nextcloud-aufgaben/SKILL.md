---
name: nextcloud-aufgaben
description: Reminder-Liste vs. Tasks-Liste in Nextcloud unterscheiden, inklusive Formatkonventionen für beide.
---

# Nextcloud – Aufgaben

Nextcloud hat mehrere Aufgabenlisten (CalDAV-basiert). Zwei werden leicht verwechselt:

- **Reminder** (Liste `Reminder`): Merkposten ohne konkrete Ausführungsschritte — etwas beobachten, später nochmal nachfragen, sich an etwas erinnern.
- **Tasks** (Liste `Tasks`): konkrete, abzuarbeitende To-dos.

Vor jedem `nextcloud_calendar_create_todo`-Aufruf erst entscheiden, ob es ein Reminder oder ein echtes Task ist, und `list_name` entsprechend setzen — der Tool-Default ist `Tasks`, für Reminder muss `list_name: Reminder` explizit gesetzt werden.

Weitere vorhandene Listen (bei thematischem Bezug nutzen, nicht pauschal Tasks): Lager, Einkaufsliste, Packliste, Miesbach, Mainz, Kirche, Server, Projekte, Cybersecurity.

## Format: Reminder

Reminder in dieser Liste beobachten typischerweise einen externen Zustand
(meist ein Upstream-Bug/Issue/PR), der sich irgendwann ändern könnte, und
tragen deshalb ein `due_date` zum periodischen Nachschauen (meist 2–8
Wochen in die Zukunft, versetzt zu bereits vorhandenen Terminen, damit
sich nicht alles am selben Tag häuft).

- **`summary`**: kurzer Titel + betroffene Komponente in Klammern, meist
  mit Issue-Nummer, z.B. `"Homeassistant Authentik Bug"` oder
  `"LiteLLM: Doppelte /v1 URL bei Opencode & anderen (Issue #26009)"`.
- **`description`**, in dieser Reihenfolge:
  1. Ein Absatz Kontext: was ist kaputt/blockiert, warum, was hängt davon ab.
  2. `Issue: <Link>` (oder `Verwandtes Issue, alternativer Lösungsweg: <Link>`
     bei einem zweiten, verwandten Ticket).
  3. `Was zu tun ist, wenn [gefixt/gemerged] ist:` gefolgt von einer
     nummerierten Liste konkreter Schritte — nicht nur "nochmal testen",
     sondern die tatsächlichen Config-Änderungen/Befehle, die dann fällig
     wären.
- Kein `priority` gesetzt.

Kurzbeispiel (gekürzt):

```
summary: "Hermes: Native Bildgenerierung ohne Drittanbieter-Key (Issue #56965)"
description: |
  Hermes' image_gen-Tool ruft immer ein separates Backend auf ... [Kontext]

  Issue: https://github.com/NousResearch/hermes-agent/issues/56965 (offen, P3, keine Kommentare)

  Was zu tun ist, wenn gefixt/gemerged ist:
  1. Hermes aktualisieren
  2. ...
due_date: "2026-09-19T08:00:01"
```

## Format: Tasks

Tasks sind deutlich informeller — echte, konkrete To-dos ohne
Beobachtungs-Charakter:

- **`summary`**: kurzer Titel, keine Issue-Nummern/Komponenten-Präfixe.
- **`description`**: oft leer, sonst freie Notizen/Stichpunkte (auch
  mal ein kompletter E-Mail-Text oder Copy-Paste-Rohmaterial) — keine
  feste Struktur, kein Issue-Link-Muster. **Description darf ruhig lang
  sein** — wer/wann/warum-Notizen reingehören, gerne vollständig. Nicht
  aus falscher Sparsamkeit kürzen.
- **kein `due_date`** — Tasks in dieser Liste sind i.d.R. offen ohne
  festen Termin. Wenn Hanno einen Termin nennt, gerne setzen, aber nie
  ohne echten Anlass.
- **Kein `priority` setzen.** Hanno pflegt Prioritäten selbständig in
  Nextcloud; eine automatische Priorisierung trifft fast nie das, was
  Hanno meint. Das Feld einfach weglassen.
