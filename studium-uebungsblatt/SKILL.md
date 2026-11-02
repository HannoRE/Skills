---
name: studium-uebungsblatt
description: Studium: Übungsblatt-Ordner, Hauptdatei und Moodle-Abgleich.
---

# Studium – Übungsblätter

Jedes Fach hat einen eigenen Ordner (`Studium/<Semester>/<Fach>/`). Darin liegen Vorlesungsmaterial und Infos direkt im Fachordner, jedes Übungsblatt bekommt einen eigenen Unterordner.

## Ordnerkonvention

- Pro Blatt ein Ordner. Die Schreibweise (`Blatt1` oder `Blatt 01`) steht in der `CLAUDE.md` des Fachs und wird dort übernommen; gibt es keine Vorgabe, gilt `Blatt1` wie in den Code-Fächern.
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
3. Fehlende Dateien holen, ohne bestehende zu überschreiben. Aufgabenblätter in den Blattordner, alles andere in den Fachordner (siehe „Dateien holen“).
4. Auffälligkeiten aus dem Überblick (Fristen, Terminänderungen, Vorlesung nur als Aufzeichnung) in `Infos.md` festhalten.

## Dateien holen

Der Moodle-MCP läuft in einem eigenen Container auf Marvin, nicht auf dem Rechner der Sitzung. `download_file` schreibt deshalb in das Dateisystem dieses Containers; ein lokaler Zielpfad schlägt mit „directory does not exist“ fehl, selbst wenn er lokal existiert. Ablauf:

1. `download_file` mit `save_to` unter `/tmp/` im Container aufrufen (Originalname beibehalten).
2. Die Datei per SSH (Alias `marvin`, siehe `ssh-gateway`) aus dem Container ziehen: `ssh marvin 'docker exec <container> cat "/tmp/<Datei>"' > "<Ziel>"`. Den Containernamen findet man mit `docker ps | grep -i moodle`. Die SSH-Banner-Zeile geht auf stderr und landet nicht in der Datei.
3. Größe und Typ prüfen (`ls -l`, `file`) und die Temp-Datei im Container löschen.
4. Große Datensätze, die nicht ins Git sollen (z. B. XML-Korpora), nach `data/` legen und bei Bedarf zusätzlich auf den Rechenserver kopieren.

Immer die Originaldatei ablegen. Keine Markdown-Abschriften von Aufgabenblättern anlegen; `fetch_file` ist nur zum Lesen da.

## Fächer mit Python-Code

Gilt für Fächer, in denen Aufgaben mit Code gelöst werden (Repo pro Fach, Ausführung auf dem Rechenserver). Die genaue Arbeitsweise steht in der `CLAUDE.md` des Fachs und hat Vorrang; hier nur die gemeinsamen Grundsätze:

- Git ist der einzige Transportweg zwischen dem lokalen Checkout (bearbeiten) und dem Checkout auf dem Rechenserver (ausführen). `results/` schreibt nur der Rechenserver. Kein Wrapper-Skript, keine Kopien per scp oder rsync.
- Hauptdatei `blattN.py` ist ein normales Skript. Ob zusätzlich ein Notebook abgegeben wird, entscheidet das Fach. Wo keins verlangt wird, kein jupytext-Format, keine `# %%`-Zellen und kein Notebook-Build.
- Texte der Abgabe (Begründungen, Zusammenfassungen, Reflexionen, Auswertungen) schreibt Hanno selbst. Claude liefert Struktur, Stichpunkte als HTML-Kommentar (vor der Abgabe löschen) und prüft Fakten und Wortzahl. Hypothesen werden vor dem Blick auf Ergebnisse formuliert.
- Stil: keine Em- oder En-Dashes, kurze Überschriften (`## Aufgabe 1`), praktisch keine Kommentare im Code, einfache ASCII-Schreibweise im Code.
- Quellen bei Aufgaben mit Medienbeitrag: Titel, Autor, Medium, Datum und Link verlangt, Alter oft begrenzt. Vor der Auswahl die Aufgabenstellung lesen, Primärquellen selbst öffnen und nur Gelesenes als gelesen angeben. Newsletter ohne Autor und öffentlichen Link taugen höchstens als Anstoß.

## Abgabe in Nextcloud eintragen

Jede Abgabe bekommt einen Task in der Nextcloud-Liste `Studium` (nicht `Tasks`). Der Task wird erst angelegt, wenn es die Abgabe wirklich gibt, also wenn das Blatt veröffentlicht ist und der Ordner dazu angelegt wird. Keine Tasks auf Vorrat für künftige Blätter.

- `summary`: `<Fach> BlattN` (Vorbild: bestehende Einträge dort).
- `due_date`: Abgabetermin, Zeitzone `Europe/Berlin`.
- `description`: Abgabeweg, Punkte, Ordner, Hauptdatei und eine Kurzliste der Aufgaben mit Stand (erledigt oder offen). Lang sein darf sie, ist der Ort, an dem „was ist noch zu tun“ verwaltet wird.
- Kein `priority` setzen.

## Chats und Interviews als Abgabe

Wenn eine Aufgabe einen KI-Chat verlangt, wird er im echten Uni-Chat geführt und unverändert als Markdown abgelegt, nicht per API simuliert. Interviews und Befragungen führt Hanno selbst, die Auswertung wird gemeinsam geschrieben.

## Zugriffsweg (agentenabhängig)

- Claude Code: Moodle- und Nextcloud-Tools über das LiteLLM-Gateway, Dateien lokal.
- Hermes und claude.ai Web: haben nicht beide Zugriff auf den lokalen Fachordner. Dann nur Task-Pflege und Planung, Dateiarbeit an Claude Code verweisen.
