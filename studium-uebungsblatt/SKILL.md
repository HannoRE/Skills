---
name: studium-uebungsblatt
description: Studium: Übungsblatt-Ordner, Hauptdatei und Moodle-Abgleich.
---

# Studium – Übungsblätter

Jedes Fach hat einen eigenen Ordner (`Studium/<Semester>/<Fach>/`). Darin liegen Vorlesungsmaterial und Infos direkt im Fachordner, jedes Übungsblatt bekommt einen eigenen Unterordner.

## Ordnerkonvention

- Pro Blatt ein Ordner `Blatt NN/` (zweistellig, z. B. `Blatt 01`).
- Hinein kommen das Aufgabenblatt (PDF aus Moodle, Originalname) und alles, was zur Lösung gehört.
- Die **Hauptdatei** heißt `BlattN.md`, `BlattN.pdf` oder `BlattN.py` (ohne führende Null, je nach Fach und Abgabeform). Nie `Lösung.md`.
- Unterlagen wie ein KI-Chat-Protokoll, Interviewnotizen oder Skizzen liegen als eigene Dateien daneben und werden in der Hauptdatei verlinkt.
- Fachweite Dokumente (Projektbeschreibung, Folien, `Infos.md` mit Terminen und Regeln) bleiben im Fachordner, nicht im Blattordner.

## Hauptdatei aufbauen

Eine Überschrift pro Aufgabe, darunter eine Überschrift pro Teilaufgabe nach der Nummerierung des Blatts. Noch nicht bearbeitete Teile stehen mit `_TODO_` drin, damit der Stand auf einen Blick sichtbar ist. Kopf: Abgabetermin und Abgabeweg.

## Moodle-Abgleich

Zu Beginn eines Blatts und bei „Habe ich alles?“-Fragen:

1. `list_courses` und `get_course_overview` für den Kurs aufrufen.
2. Die Dateien im Kursüberblick (Folien, Blätter, Projektunterlagen) mit dem lokalen Fachordner vergleichen. Videos bleiben außen vor.
3. Fehlende Dateien mit `download_file` in den passenden Ordner laden, ohne bestehende zu überschreiben. Aufgabenblätter in den Blattordner, alles andere in den Fachordner.
4. Auffälligkeiten aus dem Überblick (Fristen, Terminänderungen, Vorlesung nur als Aufzeichnung) in `Infos.md` festhalten.

## Abgabe in Nextcloud eintragen

Jede Abgabe bekommt einen Task in der Nextcloud-Liste `Studium` (nicht `Tasks`).

- `summary`: `<Fach> BlattN` (Vorbild: bestehende Einträge dort).
- `due_date`: Abgabetermin, Zeitzone `Europe/Berlin`.
- `description`: Abgabeweg, Punkte, Ordner, Hauptdatei und eine Kurzliste der Aufgaben mit Stand (erledigt oder offen). Lang sein darf sie, ist der Ort, an dem „was ist noch zu tun“ verwaltet wird.
- Kein `priority` setzen.

## Chats und Interviews als Abgabe

Wenn eine Aufgabe einen KI-Chat verlangt, wird er im echten Uni-Chat geführt und unverändert als Markdown abgelegt, nicht per API simuliert. Interviews und Befragungen führt Hanno selbst, die Auswertung wird gemeinsam geschrieben.

## Zugriffsweg (agentenabhängig)

- Claude Code: Moodle- und Nextcloud-Tools über das LiteLLM-Gateway, Dateien lokal.
- Hermes und claude.ai Web: haben nicht beide Zugriff auf den lokalen Fachordner. Dann nur Task-Pflege und Planung, Dateiarbeit an Claude Code verweisen.
