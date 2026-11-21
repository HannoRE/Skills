---
name: wissensbasis-pflege
description: Use when Hanno Wissen zur Miesbach-Wissensbasis liefert.
---

# Wissensbasis Kirche, Bunte Haus und Muggeltechnik pflegen

Hedwig (öffentlicher Wissensagent) beantwortet Fragen nur aus einem Ordner. Neues Wissen muss dort in einer festen Form landen, sonst findet sie es nicht oder gibt Internes weiter. Derselbe Ordner dient auch uns zum Nachschlagen. Es läuft nichts automatisch (kein Timer, kein Dienst, auch nicht auf Mercury): Wissen wird in einer Session abgelegt und gesichert.

## Welchen Weg gehen?

- **Mit Dateizugriff** (Claude Code, Mycroft): Wissen direkt als Datei ablegen, Abschnitt "Neues Wissen ablegen".
- **Ohne Dateizugriff** (normaler Chat): Das Wissen als Notiz in den Eingang legen, Abschnitt "Aus dem Chat". Es wird in der nächsten Session mit Dateizugriff verarbeitet.
- **Zu Beginn jeder Session mit Dateizugriff**, in der es um die Kirche geht: im Eingang nachsehen (`Wissensbasis-Betrieb/Eingang/`, vorher `git pull`) und Vorhandenes abarbeiten.

## Orte

Der Ordner `Dropbox/Zuhause/Kirche/Muggeltechnik/` ist ein Git-Repo (Remote: privates Gitea-Repo `Hanno/Wissensbasis-Kirche`). Versioniert sind zwei Unterordner:
- `Hedwig-Quellen/` (Wissensbasis, von Hedwig read-only gelesen): `Wissen/<Thema>/` (Störungen, Haus, Technik, Alltag, Menschen, Termine, Gemeinde, Planung, `_Archiv`) und `Anleitungen/<Raum>/`.
- `Wissensbasis-Betrieb/` (für Hedwig nicht sichtbar): `Regeln.md`, `Vorlage.md`, `Vorlage Störung.md`, `Quellenregister.md`, `Offene Fragen.md`, `Eingang/`, `Originale/`, `index.py`, `sichern.sh`.

Vor dem ersten Schreiben `Wissensbasis-Betrieb/Regeln.md` lesen. Sie ist maßgeblich.

## Aus dem Chat (ohne Dateizugriff)

Hanno sagt etwas wie "in der Kirche ist mir aufgefallen …" oder gibt Infos zu Technik, Räumen, Ablauf. Dann:
1. Sachlich zusammenfassen, was gesagt wurde, mit Datum und Quelle (wer hat es gesagt). Unklares nachfragen, nichts erfinden.
2. Mit dem Gitea-Werkzeug `create_or_update_file` eine Notiz anlegen: Repo `Hanno/Wissensbasis-Kirche`, Branch `main`, Pfad `Wissensbasis-Betrieb/Eingang/JJJJ-MM-TT Thema.md`. Keine Passwörter, Schlüsselcodes, Zugangsdaten oder privaten Kontaktdaten.
3. Hanno kurz bestätigen, dass es im Eingang liegt und beim nächsten Mal mit Dateizugriff eingepflegt wird.

## Drei Regeln, die immer gelten

1. Faktendokumente: kurz und prägnant, was Sache ist. Kein Stil, keine Anweisungen an Hedwig oder andere Leser, keine Hinweise auf die Ablage, keine Aussagen über den Zustand der Dokumentation.
2. Nichts Internes: Alles in der Wissensbasis kann Hedwig einem Menschen sagen. `herkunft` im Frontmatter steht in Worten ("Bewerbung zum Städtebaupreis, Oktober 2026", "Auskunft der Muggeltechnik"), nie ein Pfad, Dateiname oder eine Kapitelnummer. Der Pfad zum Original steht nur im `Quellenregister.md`. Verweise auf andere Dateien nennen den Titel. Keine Passwörter, Zugangsdaten, Schlüsselcodes, privaten Telefonnummern oder Adressen. Bei Menschen Funktionen statt Namen.
3. Nur Tatsachen aus Miesbach: Keine Abschnitte "offen" oder "unsicher", kein "vermutlich", "nicht bekannt" oder Ähnliches. Was feststeht, wird geschrieben. Was fehlt, kommt in `Offene Fragen.md` und wird Hanno gestellt, sobald es passt. Nichts raten. Quellen anderer Gemeinden (zum Beispiel Musterablaufpläne oder Unterlagen aus Mainz) gehören nicht hinein: vor dem Ablegen prüfen, ob die Quelle zu Miesbach gehört.

## Nachschlagen

`python3 -I Wissensbasis-Betrieb/index.py --suche Wort1 Wort2` zeigt alle Dateien, in denen alle Wörter vorkommen. Ohne Dateizugriff die Dateien im Gitea-Repo mit `search_repos`/`get_file_contents` lesen. Vor dem Anlegen neuer Dateien immer erst suchen, ob es das Thema schon gibt.

## Neues Wissen ablegen (mit Dateizugriff)

1. Quelle lesen (oder die Eingangsnotiz), den Inhalt in kurze Themen teilen: eine Datei pro Thema, Markdown.
2. `Vorlage.md` kopieren (für Probleme `Vorlage Störung.md`), ausfüllen, in `Wissen/<Thema>/` ablegen (Dateiname gleich Titel). `kurz` beantwortet die häufigste Frage in ein bis zwei Sätzen. Eigene Schlussfolgerungen nicht als Tatsache schreiben; im Zweifel Hanno fragen.
3. Zeile im `Quellenregister.md` ergänzen (Datei, Original, Datum, von wem). Dateien mit Leerzeichen im Namen immer in Anführungszeichen setzen oder mit dem Write-Werkzeug schreiben.
4. Verarbeitete Eingangsnotizen nach `Wissensbasis-Betrieb/Originale/Eingang/` verschieben.
5. `sh Wissensbasis-Betrieb/sichern.sh "Beschreibung"` ausführen (mit `sh`, die Dropbox startet keine Programme direkt). Es holt neue Eingaenge (`git pull`), startet `index.py` (muss "OK" melden, sonst Fehler beheben), schreibt `Wissen/INDEX.md`, durch den Hedwig die Datei findet, und committet und pusht.
6. Original nicht löschen, sondern am eigentlichen Ort lassen oder ins Archiv legen.

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
- Nichts automatisch einrichten (keine Timer oder Dienste).
- Hedwigs Repo und Marvin sind kein Ort für Wissen. Dort liegen nur Persona und Skill.
- Öffentliche Agenten (Hedwig, Petrus) schreiben nicht selbst in die Wissensbasis.
