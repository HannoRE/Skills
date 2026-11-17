---
name: wissensbasis-pflege
description: "Use when Hanno gives new knowledge about the church, Bunte Haus or Muggeltechnik in Miesbach (rooms, technology, recurring problems, routines, people, dates) or asks to add, fix or look it up: file it in the Wissensbasis so Hedwig can use it."
---

# Wissensbasis Kirche, Bunte Haus und Muggeltechnik pflegen

Hedwig (öffentlicher Wissensagent) beantwortet Fragen nur aus einem Ordner. Neues Wissen muss dort in einer festen Form landen, sonst findet sie es nicht oder gibt Internes weiter. Derselbe Ordner dient auch uns zum Nachschlagen.

## Orte (Dropbox)

Der Ordner `Dropbox/Zuhause/Kirche/Muggeltechnik/` ist ein Git-Repo (Remote: privates Gitea-Repo Wissensbasis-Kirche). Versioniert sind nur zwei Unterordner:
- `Hedwig-Quellen/` (Wissensbasis, von Hedwig read-only gelesen): `Wissen/<Thema>/` (Störungen, Haus, Technik, Alltag, Menschen, Termine, Gemeinde, Planung, `_Archiv`) und `Anleitungen/<Raum>/`.
- `Wissensbasis-Betrieb/` (für Hedwig nicht sichtbar): `Regeln.md`, `Vorlage.md`, `Vorlage Störung.md`, `Quellenregister.md`, `Offene Fragen.md`, `Eingang/`, `Originale/`, `index.py`, `sichern.sh`.

Vor dem ersten Schreiben `Wissensbasis-Betrieb/Regeln.md` lesen. Sie ist maßgeblich.

## Drei Regeln, die immer gelten

1. Faktendokumente: kurz und prägnant, was Sache ist. Kein Stil, keine Anweisungen an Hedwig oder andere Leser, keine Hinweise auf die Ablage, keine Aussagen über den Zustand der Dokumentation.
2. Nichts Internes: Alles in der Wissensbasis kann Hedwig einem Menschen sagen. `herkunft` im Frontmatter steht in Worten ("Bewerbung zum Städtebaupreis, Oktober 2026", "Auskunft der Muggeltechnik"), nie ein Pfad, Dateiname oder eine Kapitelnummer. Der Pfad zum Original steht nur im `Quellenregister.md`. Verweise auf andere Dateien nennen den Titel. Keine Passwörter, Zugangsdaten, Schlüsselcodes, privaten Telefonnummern oder Adressen. Bei Menschen Funktionen statt Namen.
3. Nur Tatsachen aus Miesbach: Keine Abschnitte "offen" oder "unsicher", kein "vermutlich", "nicht bekannt" oder Ähnliches. Was feststeht, wird geschrieben. Was fehlt, kommt in `Offene Fragen.md` und wird Hanno gestellt, sobald es passt. Nichts raten. Quellen anderer Gemeinden (zum Beispiel Musterablaufpläne oder Unterlagen aus Mainz) gehören nicht hinein: vor dem Ablegen prüfen, ob die Quelle zu Miesbach gehört.

## Nachschlagen

`python3 -I Wissensbasis-Betrieb/index.py --suche Wort1 Wort2` zeigt alle Dateien, in denen alle Wörter vorkommen. Vor dem Anlegen neuer Dateien immer erst suchen, ob es das Thema schon gibt.

## Neues Wissen ablegen

1. Quelle lesen, den Inhalt in kurze Themen teilen: eine Datei pro Thema, Markdown.
2. `Vorlage.md` kopieren (für Probleme `Vorlage Störung.md`), ausfüllen, in `Wissen/<Thema>/` ablegen (Dateiname gleich Titel). `kurz` beantwortet die häufigste Frage in ein bis zwei Sätzen. Eigene Schlussfolgerungen nicht als Tatsache schreiben; im Zweifel Hanno fragen.
3. Zeile im `Quellenregister.md` ergänzen (Datei, Original, Datum, von wem). Dateien mit Leerzeichen im Namen beim Anlegen immer in Anführungszeichen setzen oder mit dem Write-Werkzeug schreiben.
4. `sh Wissensbasis-Betrieb/sichern.sh "Beschreibung"` ausführen (mit `sh`, die Dropbox startet keine Programme direkt; auf Mercury). Es startet `index.py` (muss "OK" melden, sonst Fehler beheben), schreibt `Wissen/INDEX.md`, durch den Hedwig die Datei findet, und committet und pusht. Wer nicht auf Mercury schreibt, startet nur `index.py`; die Sicherung in Git folgt beim nächsten `sichern.sh`.
5. Original nicht löschen, sondern am eigentlichen Ort lassen oder ins Archiv legen.

## Störungen: häufige Probleme festhalten

Wenn Hanno ein wiederkehrendes Problem schildert (Ton fällt aus, LED rot, Beamer schwarz, Mikro knackt), legen wir es in `Wissen/Störungen/` ab: `symptome` (so, wie Leute es melden), `betrifft` (Gerät und Raum), Abschnitte Symptom, Ursache, Lösung, Wenn es nicht hilft und Vorfälle. Tritt ein bekanntes Problem wieder auf, nur einen Vorfall mit Datum, Raum und Ergebnis anhängen und `stand` aktualisieren. So lässt sich später auswerten, was oft vorkommt.

## Veraltetes

- Einzelner Fakt ändert sich: Datei und `stand` aktualisieren. Hat der alte Stand noch Wert, kommt er mit Datum in einen Abschnitt "Früher" derselben Datei.
- Ganze Datei überholt: nach `Wissen/_Archiv/<Thema>/` verschieben (pdf daneben mitnehmen), `status: überholt`, `gueltig_bis` und `ersetzt_durch` (Titel der neuen Datei) setzen, im Quellenregister den neuen Pfad eintragen. Der Index führt Archivdateien nur am Ende.
- Nichts löschen. Der Verlauf steht in Git.
- Abweichende Zahlen aus verschiedenen Quellen: beide nennen und kenntlich machen, welche neuer ist.
- Überschneidungen zwischen Dateien sind in Ordnung, wenn sie sich nicht widersprechen; bei Änderungen alle betroffenen Dateien nachziehen (`index.py --suche` hilft).

## Nicht tun

- Keine Passwörter oder Zugangsdaten aus Geräte-Listen übernehmen.
- Den Index nie von Hand ändern.
- Hedwigs Repo und Marvin sind kein Ort für Wissen. Dort liegen nur Persona und Skill.
- Öffentliche Agenten (Hedwig, Petrus) schreiben nicht selbst in die Wissensbasis.
