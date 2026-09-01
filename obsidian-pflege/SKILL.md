---
name: obsidian-pflege
description: Obsidian-Notizkonventionen einhalten: Struktur, Stil.
---

# Obsidian – Pflege

Vault-Wurzel: `Zuhause/Obsidian/`. Persönliche Notizen liegen in `Notizen.md`; alles, was
dauerhaft bleiben soll, ist in die vier PARA-Light-Kategorien einsortiert: `Persönlich/`
(Konstanten + Sammlungen), `Projekte/` (aktive Vorhaben), `Ressourcen/` (reines Nachschlagewerk)
und `KI Workspace/` (Input/Output für KI-Routinen, nicht für Hanno).

## Harte Regeln

- `Notizen.md` ist ausschließlich Hannos persönliche Notizfläche. Niemals dauerhafte Referenzen, Tool-Doku oder Projektinhalte dort ablegen.
- Neue thematische Inhalte gehören in einen der vier PARA-Light-Ordner: `Persönlich/` für Konstanten und persönliche Sammlungen, `Projekte/` für laufende Vorhaben mit klarem Ziel, `Ressourcen/` für reines Nachschlagewerk ohne Lebensbezug. Top-Level-Verzeichnisse `Tagebuch/` und `KI Workspace/` haben eigene Semantik (Chronologie bzw. Maschinen-Workspace) und bleiben reserviert.
- Niemals Inhalte aus `Notizen.md` automatisch verschieben; Hanno entscheidet.
- Brainstorming-Stände, Tagesdiskussionen oder Notizen, die den Stand eines Moments festhalten, gehören nicht in den Vault. Sie werden mit `YYMMDD <Begriff>.md` ins `Archiv/YYYY/` gelegt und nicht in Obsidian gepflegt. Begründung: nichts ewig Bestehendes, kein Tagebucheintrag, sondern eine Momentaufnahme, die archiviert wird. Beispiel: Vier App-Ideen für den Urlaub (Diskussion mit Papa, 20.08.2026) wurde als `Archiv/2026/260820 App Ideen für den Urlaub.md` abgelegt, nicht in `Obsidian/Projekte/`.
- **Standorte** liegen in `Persönlich/Standorte.md` (seit 2026-09-01). NICHT als Sektion in eine bestehende IT-/Infrastruktur-Notiz einbetten — Standorte sind semantisch persönliche Konstanten, nicht IT-Thema. Verwandte IT-Sicht (Serverschrank-Inventar, HA-Flächen, Tailscale-IPs) bleibt separat in `Ressourcen/Server/Netzwerk & Hardware – Physische Infrastruktur.md` und referenziert `Persönlich/Standorte.md`.

## Stil

- Doku knapp, vollständige Sätze, kein Marketing-Sprech.
- Tabellen statt Aufzählungen, wenn Inhalte ohne Beschreibung stehen bleiben.
- Codeblöcke für Befehle und Konfigausschnitte.
- Datum im Header als `## YYYY-MM-DD` oder `# YYYY-MM-DD`.

## Vault-Struktur-Migration

Stand 2026-09-01: das alte Schema (`Referenz/` für Dauer-Notizen, `Bereiche/` als
PARA-Bereichs-Kategorie, `AI Only/` als Top-Level) wurde aufgelöst zugunsten des
PARA-Light-Schemas (Persönlich / Projekte / Ressourcen / KI Workspace). Alle früheren
Wikilinks wurden aktualisiert; falls in einer Notiz noch ein alter Pfad auftaucht,
ist das ein Migrations-Bug und sollte gefixt werden (nicht stillschweigend
hingenommen).

## Operationen

- Vor jedem Schreibvorgang die Zieldatei lesen, damit der aktuelle Stand bekannt ist.
- Niemals vorhandene Inhalte überschreiben oder löschen.
- Bei Aktualisierungen Patch statt Full-Write.

## Zugriffsweg (agentenabhängig)

Claude und Hermes arbeiten auf denselben Dateien, nur über unterschiedliche Schnittstellen:

- Claude: über die Nextcloud-Notes-Tools (`nc_notes_*`). Die Nextcloud-Kategorie entspricht dem Obsidian-Ordner, der Notiztitel entspricht dem Dateinamen ohne `.md` (Beispiel: Kategorie „Tagebuch", Notiz „2026" = `Tagebuch/2026.md`).
- Hermes: direkter Dateizugriff auf den Obsidian-Vault-Pfad (`Zuhause/Obsidian/...`).
