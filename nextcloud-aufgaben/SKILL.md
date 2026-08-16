---
name: nextcloud-aufgaben
description: Reminder-Liste vs. Tasks-Liste in Nextcloud unterscheiden.
---

# Nextcloud – Aufgaben

Nextcloud hat mehrere Aufgabenlisten (CalDAV-basiert). Zwei werden leicht verwechselt:

- **Reminder** (Liste `Reminder`): Merkposten ohne konkrete Ausführungsschritte — etwas beobachten, später nochmal nachfragen, sich an etwas erinnern.
- **Tasks** (Liste `Tasks`): konkrete, abzuarbeitende To-dos.

Vor jedem `nextcloud_calendar_create_todo`-Aufruf erst entscheiden, ob es ein Reminder oder ein echtes Task ist, und `list_name` entsprechend setzen — der Tool-Default ist `Tasks`, für Reminder muss `list_name: Reminder` explizit gesetzt werden.

Weitere vorhandene Listen (bei thematischem Bezug nutzen, nicht pauschal Tasks): Lager, Einkaufsliste, Packliste, Miesbach, Mainz, Kirche, Server, Projekte, Cybersecurity.
