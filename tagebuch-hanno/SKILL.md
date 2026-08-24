---
name: tagebuch-hanno
description: Hanno-mündliche Tagebucheinträge in Obsidian schreiben.
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
- **Hannos Stil erhalten:** Formulierungen, Satzbau und Wortwahl aus dem Diktat möglichst beibehalten. Nur offensichtliche Tippfehler und Spracherkennungsartefakte korrigieren, nicht eigenmächtig „literarischer", „korrekter" oder flüssiger formulieren. Wenn Hanno einen Satz unvollständig lässt, nicht eigenmächtig ergänzen. Hannos Worte sind die Quelle der Wahrheit.
- **Nicht interpretieren.** Aussagen zu Örtlichkeiten (welcher Raum, welche Etage, welcher Ort), Gegenständen, Personen und Zeitpunkten unverändert aus dem Diktat übernehmen. Nicht eigenmächtig in andere Räume verschieben, nicht verallgemeinern, nicht „glatt" formulieren, nicht Ursachen/Zustände/Materialien/Farben/Folgen hinzudichten, die Hanno nicht genannt hat. Was Hanno nicht erwähnt, wird nicht eigenmächtig ergänzt — auch keine „offensichtlichen" Details. Beispiel: Sagt Hanno „die Fliesen hängen im Vorzimmer, wo ich schlafe, weil da früher ein Ofen stand", steht da weder „im Bad" noch „im Vorzimmer, wo wir schlafen", und es wird nicht „originale Fliesen von 1896" o. ä. hinzugefügt, wenn Hanno es nicht gesagt hat.

## Technische Regeln

- Vor jedem Schreibvorgang die Jahresdatei lesen, damit der aktuelle Stand bekannt ist.
- Tagesblöcke stehen immer unter dem Header `## YYYY-MM-DD` (z. B. `## 2026-08-23`), absteigend sortiert mit dem neuesten Tag am Dateiende.
- Nur anhängen, niemals vorhandene Inhalte überschreiben oder löschen.
- Falls die Jahresdatei fehlt, neu anlegen mit Header `# 2026` und kurzer Boilerplate.
- **Patch-Tool-Falle: deutsche Anführungszeichen.** Beim `patch`-Tool MUSS `old_string` exakt die Unicode-Zeichen aus der Datei enthalten (Typ 66/99 in deutschen Texten) — ASCII-Anführungszeichen matchen nicht. Bei Fehlschlag zuerst die Originalstelle mit `read_file` laden und 1:1 kopieren, nicht „korrigieren".

## Zugriffsweg (agentenabhängig)

- Claude: Nextcloud-Notes-Kategorie „Tagebuch", Notiztitel = Jahr (z. B. „2026"). Allgemeine Zuordnung siehe `obsidian-pflege`.
- Hermes: `Zuhause/Obsidian/Tagebuch/<jahr>.md` direkt.
