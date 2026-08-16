---
name: tagebuch-hanno
description: Workflow für Hanno-mündliche Tagebucheinträge. Verwenden bei jeder Anfrage, persönliche Tagesnotizen in Obsidian zu führen oder zu überarbeiten.
---

# Tagebuch – Hanno

Tagebuch wird in Jahresdateien geführt (`2026.md`, `2027.md` usw.) im Obsidian-Ordner `Tagebuch/`. Jeder Tag bekommt einen `## YYYY-MM-DD`-Header. Neue Einträge werden ausschließlich angehängt, niemals überschrieben.

## Standard-Workflow

1. Hanno diktiert mündlich oder stichpunktartig, was am Tag passiert ist.
2. Agent formuliert: zusammenhängender, persönlicher Fließtext in Hanno-Sprache. Keine Stichpunkte, keine Literaturprosa.
3. Sammelappend: Agent hängt alle fertigen Einträge in einem Schwung an die Jahresdatei, jeweils ein eigener `## YYYY-MM-DD`-Block.

Wichtig: Niemals Einträge versenden, ergänzen oder verschieben, bevor Hanno die finalen Texte freigegeben hat. Nicht ohne Rückfrage vorformulieren oder schreiben.

## Regeln für die Formulierung

- Vollständige Sätze, mündlicher Ton.
- Spracherkennungsfehler glätten, aber keine substantiellen Ergänzungen ausdenken.
- Keine Fremd-Aussagen in den Mund legen, nur was Hanno tatsächlich gesagt hat.
- Zeitliche Reihenfolge im Eintrag entspricht dem Tagesverlauf.

## Technische Regeln

- Vor jedem Schreibvorgang die Jahresdatei lesen, damit der aktuelle Stand bekannt ist.
- Nur anhängen, niemals vorhandene Inhalte überschreiben oder löschen.
- Falls die Jahresdatei fehlt, neu anlegen mit Header `# 2026` und kurzer Boilerplate.

## Zugriffsweg (agentenabhängig)

- Claude: Nextcloud-Notes-Kategorie „Tagebuch", Notiztitel = Jahr (z. B. „2026"). Allgemeine Zuordnung siehe `obsidian-pflege`.
- Hermes: `Zuhause/Obsidian/Tagebuch/<jahr>.md` direkt.
