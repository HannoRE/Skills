---
name: tagebuch-hanno
description: Hanno-mündliche Tagebucheinträge in Obsidian schreiben.
---

# Tagebuch – Hanno

Tagebuch wird in Jahresdateien geführt (`2026.md`, `2027.md` usw.) im Obsidian-Ordner `Tagebuch/`. Jeder Tag bekommt einen `## YYYY-MM-DD`-Header. Neue Einträge werden ausschließlich angehängt, niemals überschrieben.

## Standard-Workflow

**Strenge Reihenfolge einhalten — Hanno hat das in der Praxis mehrfach explizit so eingefordert:**

1. Hanno diktiert mündlich oder stichpunktartig, was am Tag passiert ist.
2. **Hermes liest die Jahresdatei**, prüft, ob `## YYYY-MM-DD` schon existiert (siehe „Retro-/Erweitern-Pitfall" unten), und scannt das Diktat auf interpretationsbedürftige Stellen.
3. **Hermes gibt Hanno alle ungesicherten / interpretationsbedürftigen Stellen mit Satz-Kontext** (1–3 Zeilen drumherum, was vor und nach der Stelle steht) **und fragt nach Klärung**. Pro Stelle: was im Diktat steht + meine beste Vermutung (falls vorhanden) + „Bitte klären oder stehen lassen".
4. Hanno korrigiert die Stellen — direkt im Chat, kurze Bestätigung pro Punkt reicht.
5. **Erst dann** schreibt Hermes den Eintrag: zusammenhängender, persönlicher Fließtext in Hanno-Sprache. Keine Stichpunkte, keine Literaturprosa, aber **kohärent geglättet** (Abbrüche zu Ende führen, Füllwörter moderat raus, Wortwiederholungen streichen, „und"-Satz-Anfänger glätten).
6. Sammelappend: Agent hängt alle fertigen Einträge in einem Schwung an die Jahresdatei, jeweils ein eigener `## YYYY-MM-DD`-Block. Bei bereits existierendem Block siehe „Retro-/Erweitern-Pitfall".

Wichtig: **Schritt 5 (Schreiben + Glätten) erfolgt strikt NACH Schritt 4 (Korrekturen)**. Niemals vorformulieren und dann um Korrektur bitten. Hanno hat explizit klargestellt: „Vor Eintrag: Kontext zeigen, Hanno korrigiert, DANN Eintrag schreiben + glätten."

**Stil-Erhalt beim Glätten:** Hanno brabbelt beim Diktieren — der Eintrag wird **kohärent**, aber **nicht literarisch glatt**. Persönlicher Sprachstil, Wortwahl, deutsche Ortsnamen-Formen (z. B. „Kutenberg" statt „Kutná Hora"), originäre Wendungen bleiben erhalten. Hanno hat das so formuliert: „Nur vielleicht kann man Teilweise meinen Stil erhalten" — d. h. nicht jeder Original-Satz bleibt 1:1, aber Stil-Eigenheiten (Lieblingswörter, Sprachmelodie, leichte Umständlichkeit) gehen nicht verloren.

Niemals Einträge versenden, ergänzen oder verschicken, bevor Hanno die finalen Texte freigegeben hat.

**Push-Pitfall:** Nach jeder Skill-Änderung (egal ob per `patch` + Edit oder neu geschrieben) muss `git push origin main` laufen — das lokale Repo zu `~/repos/Hanno/Skills/` ist **nicht** automatisch synchron mit `origin/main`. Verifikation: `git status` (sollte „up to date with 'origin/main'" zeigen) und/oder `git log origin/main..main` (sollte leer sein). Hanno hat schon zweimal nachfragen müssen, weil Commits lokal blieben.

**Retro-/Erweitern-Pitfall:** Wenn Hanno rückwirkend für vergangene Tage diktiert (z. B. „am Freitag sind wir losgekommen, dann Sonntag Ankunft in Marienbad“), können bestehende `## YYYY-MM-DD`-Einträge am falschen Datum stehen (Seeadler-Eintrag war z. B. auf 22.08. datiert, gehört aber zu 23.08. der Anreise). Vor dem Anlegen: **immer erst alle bestehenden Header in der Datei scannen** (`grep '^## ' <datei>`) und Hanno kurz fragen, wenn ein Datum nicht zum Inhalt passt. Nichts eigenmächtig umdatieren.

**Erweitern vs. neuer Eintrag:** Wenn der Tag, für den Hanno diktiert, **bereits einen Eintrag** in der Jahresdatei hat (auch wenn er nur aus einem Satz oder einer Notiz besteht), liegt ein **Erweitern-Fall** vor — kein komplett neuer Eintrag. Vorgehen:
1. **Bestehenden Inhalt des Tagesblocks komplett lesen**, Hanno kurz zeigen, was schon drinsteht.
2. Hanno entscheidet: ist das neue Diktat eine **Ergänzung** zum bestehenden Eintrag, oder **ersetzt/überschreibt** es ihn?
3. Bei Ergänzung: bestehender Text + neuer Text werden im Workflow zu **einem** Eintrag verschmolzen, dann geglättet — nicht zwei Blöcke unter dem gleichen Datum.
4. Bei Ersetzung: vor dem Überschreiben den alten Inhalt 1:1 zitieren und explizit fragen, ob er weg kann. Nichts eigenmächtig löschen.

Beispiel aus der Praxis (25.08.2026): Der 24.08.-Eintrag existierte bereits mit Sauna-Detail; Hanno diktierte zwei Tage später rückwirkend den ganzen Reiseanfang. Statt einen neuen 24.08.-Block zu schreiben, wurde der bestehende erweitert.

**Backup-Dropbox-Pitfall:** Wenn der `dbx`-Wrapper (`~/.local/bin/dbx`) plötzlich ohne Fehler-Output schweigt und nichts schreibt, ist der Access-Token abgelaufen (4h-Grenze). Der Wrapper refresht eigentlich automatisch, ABER wenn das Auto-Refresh versagt (z.B. weil das Skript `set -e` drin hat), gibt es stillschweigend ein leeres Ergebnis. **Workaround:** Erst manuell `dbx account` (zeigt Account-Daten, refresht dabei) oder `dbx get ...` einfach nochmal laufen lassen — der zweite Aufruf funktioniert dann. Langfristig ist der `dbx`-Wrapper im Repo `dotfiles/local/bin/dbx` oder ähnlich — wenn er crasht, siehe `references/dbx-wrapper-recipes.md`.

**Pitfall — Hardcoded-Credentials im Wrapper (2026-09-01):** Der `dbx`-Wrapper hatte `CLIENT_ID` und `CLIENT_SECRET` fest im Skript verdrahtet. Nach einer App-Rotation standen in `~/.config/dbxcli/credentials.json` neue Werte, aber der Wrapper refreshte weiter mit den alten hardcoded Werten. Ergebnis: jede Dropbox-Aktion schlug mit „app is disabled" fehl, **obwohl** der manuelle Refresh per `curl` mit den JSON-Werten funktionierte. Diagnose dauerte, weil `dbx account` plausibel aussah (refresh schien zu gehen), aber der eigentliche API-Call dann401 kassierte.

**Fix:** Wrapper liest jetzt `client_id`/`client_secret` aus `credentials.json`. Bei jeder App-Rotation reicht es, die JSON neu zu schreiben — kein Wrapper-Edit.

**Faustregel:** **Credentials haben genau eine Quelle.** In der Regel die JSON/INI/TOML-Config-Datei, nie das Skript. Wenn ein Wrapper Credentials fest verdrahtet, ist das ein latenter Bug, der spätestens bei der nächsten Rotation zuschlägt. Beim Code-Review eines Wrappers als Erstes prüfen: gibt es eine Quelle, oder sind Werte hardcoded?

## Regeln für die Formulierung

- **Ziel: glatter, kohärenter Fließtext.** Hanno diktiert mündlich (Brabbel, Abbrüche, Füllwörter, Wortwiederholungen, Sätze fangen neu an). Hermes macht daraus **lesbaren, zusammenhängenden Text**. Das ist die Kernaufgabe — nicht „nur STT korrigieren". Ohne diesen Schritt ist der Eintrag nicht brauchbar.
- Vollständige Sätze, mündlicher Ton.
- Spracherkennungsfehler glätten, aber keine substantiellen Ergänzungen ausdenken.
- Keine Fremd-Aussagen in den Mund legen, nur was Hanno tatsächlich gesagt hat.
- Zeitliche Reihenfolge im Eintrag entspricht dem Tagesverlauf.
- **Zwei Achsen – strikt getrennt halten:**
  - **Korrigieren (JA):** STT-Aussetzer, Spracherkennungsfehler, Vertipper, Wortverdreher, Verdopplungen, falsche Verbformen, falsche Artikel/Kasus/Konjugationen, abgebrochene Wörter. Diese entstehen durch das Diktat selbst, nicht durch Hanno. Was eindeutig ein STT- oder Diktier-Artefakt ist, wird repariert — drei Sauna-Durchgänge, bis es lesbar ist, ohne Hannos Stimme zu verfälschen. Beispiele: „römischen Bart" → „römischen Bad", „hirichliche Sauna" → **NICHT ändern — „Sauna" reicht, das ist normales Sauna-Brauchtum, kein Adjektiv nötig**, „Boglocket" → „Loket", „Borg-Lock-Hit" → „Burg Loket", „Schwimmbäck" → „Schwimmbecken" (STT-Aussetzer, **kein** Stilmerkmal), „Sträge" → „Straße", „Badamangel" → „Bademantel", „Kuhhotel" → „Kurhotel", „hat es aufgefallen" → „ist es aufgefallen", „die Hotel ist" → „das Hotel ist", „haben einen ewig langer Marsch" → „war ein ewig langer Marsch", „im Im Prinzip" → „im Prinzip", „um Lätt" → „live" / kontextspezifisch („live zubereitetes Omelett"), „nicht ziemlich wach" → „nicht besonders" (bei Obst/Gebäck).
  - **Stil erhalten (generell) — ABER kohärenter Text:** Hannos persönlicher Sprachstil (Wortwahl, deutsche Ortsnamen-Formen wie „Kutenberg" statt „Kutná Hora", originäre Wendungen) bleibt erhalten — aber der Eintrag wird **deutlich geglättet**. Hanno brabbelt beim Diktieren (Abbrüche, Füllwörter, Sätze fangen neu an, „und" als Satz-Anfänger, Wortwiederholungen, unvollständige Sätze). Hermes macht daraus **kohärenten Text** mit klarem Satzbau, ohne Hannos Stimme zu verlieren: abgebrochene Sätze werden zu Ende geführt, Füllwörter moderat raus, Wortwiederholungen gestrichen. Aber: keine fremden Formulierungen, keine literarische Glättung, keine inhaltliche Ergänzung. Was Hanno inhaltlich gemeint hat, bleibt unangetastet. Was STT verhunzt hat, wird repariert.

- **Verbatim-Auslieferung mit Kontext-Hinweisen.** Nach jedem Transkript/Upload eines Eintrags immer kurz die Stellen markieren, die interpretationsbedürftig oder ungesichert sind — mit bis zu 3 Zeilen Kontext drumherum (was vor und nach der Stelle steht) — damit Hanno sie beim Gegenlesen direkt korrigieren kann. Nicht raten, was Hanno gemeint hat; stattdessen die Roh-Stelle sichtbar lassen oder als „Vermutung X / Alternative Y" markieren.

**Pre-Push-Quality-Gate (verbindlich vor jedem `dbx put`).** Vor jedem Upload eines Eintrags die folgenden fünf Checks explizit durchgehen — wenn auch nur einer danebengeht, nicht pushen, sondern Hanno die Stelle(n) als Kontext-Liste vorlegen:

1. **STT-Reste-Scan:** Alle bekannten STT-Aussetzer (siehe unten) entfernt? Speziell: „Modell aus", „Badamangel", „Kuhhotel", „Schwimmbäck", „um Lätt", „nicht ziemlich wach", „Borg-Lock-Hit", Eigennamen mit Phantasiebuchstaben.
2. **Abrupte-Abbrüche-Scan:** Kein Satz endet mitten im Gedanken („haben erst mal in Loket gehalten." → womit? warum? was kam dann?). Entweder Satz sinnvoll zu Ende führen (mit was aus dem Kontext logisch folgt) ODER ehrlichen `[…] folgt`-Marker setzen. Niemals halbe Sätze stehen lassen.
3. **Ankündigungs-Scan:** Wurde im Eintrag etwas angekündigt (z.B. „haben in Loket gehalten", „am Nachmittag gingen wir dann ..."), aber dann nicht ausgeführt? Ankündigungen einlösen oder den Satz umformulieren, sodass nichts offen bleibt. Wenn Hanno über ein Event / einen Ort geredet hat, MUSS er im Eintrag auftauchen — entweder beschrieben oder mit ehrlichem „[Loket: folgt]"-Marker.
4. **Sinn-Schnitzer-Scan:** Kein Quatsch wie „in einer schönen Höhe gebaut" (gemeint war wahrscheinlich Hang-Lage), „dem Prinzip halbes Krankenhaus" (ohne Artikel), „im Prinzip schon mal abgelaufen" (zweideutig). Solche Stellen vor dem Push umformulieren oder Hanno fragen.
5. **Eigenname-Scan:** Bei Stadt-/Ortsnamen, die Hanno deutsch ausspricht („Kutenberg", „Karlsbad", „Mariebad"), ist die tschechische Originalform **nicht** eindeutig aus dem STT rekonstruierbar. **Nicht eigenmächtig auf tschechische Form umschreiben** — entweder Hannos Schreibweise übernehmen oder ihn vorher fragen. Hanno hat z.B. „Kutenberg"→„Kutná Hora" selbst korrigiert; das war nicht meine Aufgabe. Ausnahme: Wenn der Eigenname eindeutig falsch ist (z.B. „Beirut" statt „Bayreuth" bei Wagner-Kontext), frag vorher.

Wenn Hanno den Eintrag manuell repariert hat (er hat das z.B. am 26.08.2026 mit dem 24.08.-Eintrag getan — Schaden, der nicht hätte sein dürfen), ist die Lektion: der Push hätte gar nicht erst stattfinden dürfen. Lieber einmal mehr nachfragen als Schaden anrichten.

- **Bekannte STT-Aussetzer, deren Korrektur sicher ist (einfach anwenden):**
  - „Schwimmbäck" → „Schwimmbecken" (STT-Aussetzer, **kein** Stilmerkmal)
  - „um Lätt" → „live" (Omelett-Station-Kontext: „live zubereitetes Omelett")
  - „nicht ziemlich wach" → „nicht besonders" (bei Obst/Gebäck)
  - „Boglocket"/„Borg-Lock-Hit" → „Loket"/„Burg Loket"
  - „Bademantel" für „Badamangel"
  - „Kurhotel" für „Kuhhotel"
  - „F&F"/„Faith & Fire" → nicht zu „Rise" verkürzen (z. B. Kalendereintrag am 13.09.) — STT-Aussetzer bei kurzen englischen Markennamen
  - Eigennamen (Lennart, Apaleo, Bayreuth statt Beirut) — **niemals raten**, immer Hanno fragen oder verbatim mit Kontext-Marker lassen

- **Normales Sauna-/Wellness-Brauchtum NICHT interpretieren:** Drei Durchläufe Sauna mit Kaltwasserbecken, römisches Bad, ruhige Atmosphäre — das ist Standard, einfach so hinschreiben ohne Kommentar oder Wertung. Kein „richtige Sauna" (das ist STT-Fehlinterpretation von „kirchlich"), keine Erklärung warum dreimal — Hanno weiß was er gemacht hat.
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
