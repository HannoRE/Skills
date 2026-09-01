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

### Migrations-Workflow (wenn Hanno eine Vault-Umstrukturierung anstößt)

Bewährte Reihenfolge — in der Praxis am 2026-09-01 durchgespielt:

1. **Bestandsaufnahme.** Erst alle Top-Level-Ordner auflisten (`ls -1 -d */`), dann in jeden Ordner schauen, was drin ist. Faustregel: ein Ordner mit 0–2 Dateien ist verdächtig — entweder frisch angelegt und leer, oder eine vergessene Nische.
2. **Mapping-Plan als Datei ablegen.** NICHT sofort umziehen. Erst eine `.umbau-mapping.md` im Vault anlegen mit der Tabelle „Von → Nach / Warum". Das ist die Denkpause und macht den Plan sichtbar für Hanno, bevor irgendwas verschoben wird.
3. **Clarify der offenen Fragen.** Wenn mehrere plausible Mappings existieren (z. B. „kommt KI-Agenten nach Persönlich oder als eigener Top-Level?"), mit `clarify` nachfragen. Bei der Migrationsentscheidung sind oft mehrere Wege sinnvoll; die Wahl sollte begründet sein, nicht default.
4. **Atomar umbenennen.** `os.rename(src, dst)` statt `shutil.copy2`+`os.remove`. Reason: `copy2` ruft `copystat` auf, das bei gerade-von-Dropbox-aktualisierten Dateien race-conditiont mit `OSError: [Errno 5] Input/output error`. `os.rename` ist atomar auf demselben Filesystem und umgeht den read+write-Pfad.
5. **Leere Ordner aufräumen.** Wenn ein Quellordner nach allen Moves leer ist, löschen (sonst Geisterordner).
6. **Wikilinks in einem Rutsch updaten.** Grep nach `\.\./<alterOrdner>/`, `\.\./<alterOrdner>/`, `<alterOrdner>/` in allen Wikilinks. Plain-Text-Treffer (Beschreibungen alter Struktur in Doku-Notizen) NICHT vergessen — die muss man mit Sinn ersetzen, nicht stumm durchgehen lassen.
7. **README der migrierten Ordner anpassen.** Header und „Verwandt"-Abschnitt auf neue Pfade. Migrations-Notiz („Pfad seit YYYY-MM-DD: … vorher unter ALT/") bewusst behalten — sie dokumentiert, warum der Ordner mal woanders war.
8. **Vault-Doku updaten.** Wenn es eine Notiz gibt, die die Vault-Struktur selbst dokumentiert (typisch: `Ressourcen/Server/Dropbox Ordnerstruktur.md`), Sektion Vault mit dem neuen Schema überschreiben + Migrations-Hinweis anhängen.
9. **Pre-Commit-Gate.** Wenn das Repo betroffen ist (Skills, Sync-Skripte): Grep über `git diff --cached` nach alten Pfad-Patterns, dann commit + push. Beispiel: `obsidian-pflege/SKILL.md` musste gepatcht werden, weil die Default-Speicherort-Regel auf `Referenz/` zeigte.
10. **Cloud-Sync triggern.** Vault-Änderungen sind erst sichtbar, wenn die geänderten Notizen per `dbx put` zurück in die Dropbox geschoben werden. Dropbox synct **nicht** automatisch in die andere Richtung (lokal → cloud muss explizit).

### Anti-Patterns (was Hanno explizit nicht will)

- **Standort-Info als Sektion in eine IT-Notiz einbetten.** Hanno hat am 2026-09-01 explizit widersprochen: Standorte sind persönliche Konstanten, nicht IT. Lieber eigene `Persönlich/Standorte.md` mit gegenseitigem Verweis auf die IT-Sicht in `Ressourcen/Server/`.
- **Tags statt Struktur.** User-Meinung: PARA-light-Top-Level-Ordner sind besser als ein Wust von Tags, weil sichtbar beim Öffnen ohne Plugin-Abhängigkeit. Tags nur für Querschnitte (z. B. `#reise`, `#2026`) zusätzlich zur Ordner-Struktur, nicht statt.
- **Zahlen-Präfixe (`0 Persönlich/`, `1 Projekte/`).** User-Präferenz vom 2026-09-01: Ordnernamen ohne Zahlen, weil alphabetische Sortierung in Obsidian ohnehin automatisch passiert und Zahlen visuell stören.

## Operationen

- Vor jedem Schreibvorgang die Zieldatei lesen, damit der aktuelle Stand bekannt ist.
- Niemals vorhandene Inhalte überschreiben oder löschen.
- Bei Aktualisierungen Patch statt Full-Write.

## Zugriffsweg (agentenabhängig)

Claude und Hermes arbeiten auf denselben Dateien, nur über unterschiedliche Schnittstellen:

- Claude: über die Nextcloud-Notes-Tools (`nc_notes_*`). Die Nextcloud-Kategorie entspricht dem Obsidian-Ordner, der Notiztitel entspricht dem Dateinamen ohne `.md` (Beispiel: Kategorie „Tagebuch", Notiz „2026" = `Tagebuch/2026.md`).
- Hermes: direkter Dateizugriff auf den Obsidian-Vault-Pfad (`Zuhause/Obsidian/...`).
