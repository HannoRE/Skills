---
name: obsidian-pflege
description: Obsidian-Notizkonventionen einhalten: Struktur, Stil.
---

# Obsidian – Pflege

Vault-Wurzel: `Zuhause/Obsidian/`. Persönliche Notizen liegen in `Notizen.md`, dauerhafte Referenzen in `Referenz/`, thematische Inhalte in `Kultur/`, `Server/`, `Apaleo/` usw.

## Harte Regeln

- `Notizen.md` ist ausschließlich Hannos persönliche Notizfläche. Niemals dauerhafte Referenzen, Tool-Doku oder Projektinhalte dort ablegen.
- Dauerhafte Referenzen gehören unter `Referenz/` oder in einen passenden thematischen Ordner (`Kultur/Bayreuth/`, `Server/`, `Apaleo/`).
- Themenordner-Inhalte (zum Beispiel „Wagner Sitzplätze Bayreuth") immer in den passenden Themenordner, nicht in `Referenz/`.
- Niemals Inhalte aus `Notizen.md` in `Referenz/` automatisch verschieben; Hanno entscheidet.

## Stil

- Doku knapp, vollständige Sätze, kein Marketing-Sprech.
- Tabellen statt Aufzählungen, wenn Inhalte ohne Beschreibung stehen bleiben.
- Codeblöcke für Befehle und Konfigausschnitte.
- Datum im Header als `## YYYY-MM-DD` oder `# YYYY-MM-DD`.

## Operationen

- Vor jedem Schreibvorgang die Zieldatei lesen, damit der aktuelle Stand bekannt ist.
- Niemals vorhandene Inhalte überschreiben oder löschen.
- Bei Aktualisierungen Patch statt Full-Write.

## Zugriffsweg (agentenabhängig)

Claude und Hermes arbeiten auf denselben Dateien, nur über unterschiedliche Schnittstellen:

- Claude: über die Nextcloud-Notes-Tools (`nc_notes_*`). Die Nextcloud-Kategorie entspricht dem Obsidian-Ordner, der Notiztitel entspricht dem Dateinamen ohne `.md` (Beispiel: Kategorie „Tagebuch", Notiz „2026" = `Tagebuch/2026.md`).
- Hermes: direkter Dateizugriff auf den Obsidian-Vault-Pfad (`Zuhause/Obsidian/...`).
