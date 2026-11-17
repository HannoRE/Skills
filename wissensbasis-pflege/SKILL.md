---
name: wissensbasis-pflege
description: "Use when Hanno gives new knowledge about the church, Bunte Haus or Muggeltechnik (rooms, technology, recurring problems, routines, people, dates) or asks to add, fix or look it up: file it in the Wissensbasis so Hedwig can use it."
---

# Wissensbasis Kirche, Bunte Haus und Muggeltechnik pflegen

Hedwig (öffentlicher Wissensagent) beantwortet Fragen nur aus einem Ordner. Neues Wissen muss dort in einer festen Form landen, sonst findet sie es nicht oder gibt Internes weiter. Derselbe Ordner dient auch uns zum Nachschlagen.

## Orte (Dropbox)

- Wissensbasis, von Hedwig read-only gelesen: `Dropbox/Zuhause/Kirche/Muggeltechnik/Hedwig-Quellen/` mit `Wissen/<Thema>/` (Störungen, Haus, Technik, Alltag, Menschen, Termine, Gemeinde, Planung) und `Anleitungen/<Raum>/`.
- Betrieb, für Hedwig nicht sichtbar: `Dropbox/Zuhause/Kirche/Muggeltechnik/Wissensbasis-Betrieb/` mit `Regeln.md`, `Vorlage.md`, `Vorlage Störung.md`, `Quellenregister.md`, `Eingang/` und `index.py`.

Vor dem ersten Schreiben `Wissensbasis-Betrieb/Regeln.md` lesen. Sie ist maßgeblich.

## Die eine Regel

Alles in der Wissensbasis kann Hedwig einem Menschen sagen. Deshalb steht dort nichts Internes:
- `herkunft` im Frontmatter in Worten ("Bewerbung zum Städtebaupreis, Oktober 2026", "Auskunft der Muggeltechnik"), nie ein Pfad, Dateiname oder eine Kapitelnummer.
- Der Pfad zum Original steht nur im `Quellenregister.md`.
- Verweise auf andere Dateien nennen den Titel, nicht den Pfad.
- Keine Passwörter, Zugangsdaten, privaten Telefonnummern oder Adressen. Bei Menschen Funktionen statt Namen.

## Nachschlagen

`python3 -I Wissensbasis-Betrieb/index.py --suche Wort1 Wort2` zeigt alle Dateien, in denen alle Wörter vorkommen. Dann die Datei lesen. Vor dem Anlegen neuer Dateien immer erst suchen, ob es das Thema schon gibt.

## Neues Wissen ablegen

1. Quelle lesen, den Inhalt in kurze Themen teilen: eine Datei pro Thema, Markdown.
2. `Vorlage.md` kopieren (für Probleme `Vorlage Störung.md`), ausfüllen, in `Wissen/<Thema>/` ablegen (Dateiname gleich Titel). `kurz` beantwortet die häufigste Frage in ein bis zwei Sätzen. Unsicheres unter "Offen oder unsicher" nennen und nicht raten. Keine Ableitungen als Tatsachen ausgeben.
3. Zeile im `Quellenregister.md` ergänzen (Datei, Original, Datum, von wem).
4. `python3 -I Wissensbasis-Betrieb/index.py` ausführen. Es muss "OK" melden, sonst Fehler beheben. Es schreibt `Wissen/INDEX.md`, über den Hedwig die Datei findet.
5. Original nicht löschen, sondern am eigentlichen Ort lassen oder ins Archiv legen.

## Störungen: häufige Probleme festhalten

Wenn Hanno ein wiederkehrendes Problem schildert (Ton fällt aus, LED rot, Beamer schwarz, Mikro knackt), legen wir es in `Wissen/Störungen/` ab: `symptome` (so, wie Leute es melden), `betrifft` (Gerät und Raum), Abschnitte Symptom, Ursache, Lösung, Wenn es nicht hilft und Vorfälle. Tritt ein bekanntes Problem wieder auf, nur einen Vorfall mit Datum, Raum und Ergebnis anhängen und `stand` aktualisieren. So lässt sich später auswerten, was oft vorkommt.

## Ändern und Widersprüche

- Aussage ändert sich: Datei und `stand` aktualisieren, Index neu erzeugen.
- Neues widerspricht Altem: Alte Datei auf `status: überholt` setzen. Bei Zahlen beide Angaben festhalten und die neuere kenntlich machen. Nie stillschweigend löschen.
- Eine Auskunft von Hanno ist eine Quelle wie jede andere: `herkunft` "Auskunft der Muggeltechnik", im Register vermerken, wann gesagt.

## Nicht tun

- Keine Passwörter oder Zugangsdaten aus Geräte-Listen übernehmen.
- Den Index nie von Hand ändern.
- Hedwigs Repo und Marvin sind kein Ort für Wissen. Dort liegen nur Persona und Skill.
- Öffentliche Agenten (Hedwig, Petrus) schreiben nicht selbst in die Wissensbasis.
