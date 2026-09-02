---
name: dbx-wrapper-recipes
description: Dropbox von Headless-Linux aus nutzen — dbxcli + OAuth-Wrapper, Setup-Skizze plus bekannte Bugfix-Recipes (sanitized).
---

# Dropbox-CLI: Headless-Setup + Bugfix-Recipes (sanitized)

Kurzfassung für den nächsten Tag, an dem Dropbox von einem Server aus gebraucht wird, plus die bisher aufgetretenen Bugs/Fixes. Ausführliche Werte, App-Key, Token-Dateien und Pfade zur persönlichen Config sind **lokal** — gehören nicht in ein öffentliches Skill-Repo.

## Was es gibt

Zwei Bausteine:

- **`dbxcli`** — der offizielle Dropbox-CLI. Liest Token aus einer lokalen JSON-Datei (`~/.config/dbxcli/credentials.json`) und refresht es alle 4 h automatisch. Quellcode unter <https://github.com/dropbox/dbxcli>.
- **eigener Wrapper** (`~/.local/bin/dbx`) — kompensiert ein häufiges `dbxcli`-Problem: Pfade mit Sonderzeichen und Umlauten werden anders behandelt als im offiziellen Client. Außerdem erzwingt er den Storage-Root `/Zuhause` (nicht `/Zuhause/snapshot` wie in der Dropbox-Web-UI).

Beide sind lokal installiert, nicht in diesem Repo.

## Mechanik

- Wrapper liest `~/.config/dbxcli/credentials.json` (enthält `access_token` + `refresh_token` + `client_id` + `client_secret`).
- Access-Token läuft nach 4 Std ab, Refresh-Token hält ~4 Jahre.
- Der Wrapper refresht selbst (nicht via dbxcli) — App Key + Secret liest er aus `credentials.json`, nicht hardcoded (siehe Bug 1 unten, warum das wichtig ist).

## Wie OAuth läuft (4 Schritte, generisch)

1. **App anlegen** auf `https://www.dropbox.com/developers/apps` — „Scoped access", „App folder" oder „Full Dropbox". Permissions: `files.content.read/write`, `files.metadata.read/write`, `account_info.read`.
2. **Redirect-URI eintragen** (in den App-Settings unter OAuth 2): z. B. `http://127.0.0.1:8484/callback`. Ohne genau diesen Eintrag kommt `Invalid redirect_uri`.
3. **PKCE-Authorization-URL öffnen** mit `client_id`, `redirect_uri`, `scope`, `code_challenge` (SHA256 eines frischen `code_verifier`), `code_challenge_method=S256`, `token_access_type=offline`.
4. **Code aus Redirect-URL abgreifen** (`?code=…`), mit Code + Verifier gegen `https://api.dropboxapi.com/oauth2/token` tauschen → liefert `access_token` (4 h gültig) **und** `refresh_token` (4 Jahre gültig bei `offline`).

Falls die Redirect-URI nicht in der App konfiguriert werden kann: als Workaround in der Dev-Konsole unter „Generated access token" einen kurzen Token generieren (4 h gültig) und in `credentials.json` eintragen — sauberer OAuth geht danach trotzdem.

## Bekannte Bugs / Fixes

### Bug 1: Stille Outputs, keine Schreibvorgänge (✅ finaler Fix im Wrapper)

**Symptom:** `dbx get …` und `dbx put …` geben **keinen Fehler** aus und schreiben/laden **nichts** — der Output ist einfach leer. Exit-Code 0 oder ohne klares Signal.

**Ursache:** Der Wrapper hatte eine frühe Version mit `set -e` und einem fehlerhaften Refresh-Pfad. Wenn der Access-Token abläuft, schlägt `dbxcli` mit „expired_access_token" fehl, der Refresh-Path aber wird durch `set -e` vorzeitig abgebrochen, **bevor** er den eigentlichen Befehl nochmal probiert.

**Finaler Fix (jetzt im Wrapper, August 2026):**
- **KEIN `set -e`** im Wrapper — bewusste Trennung: Token-Read, Befehl, ggf. Refresh, Re-Run.
- Access- und Refresh-Token werden in **lokale Shell-Variablen** geladen, dann `DBXCLI_ACCESS_TOKEN="..." dbxcli ...` aufgerufen.
- `DBXCLI_OUTPUT` und `DBXCLI_EXIT` werden separat erfasst.
- Wenn Exit≠0 UND Output enthält „401"/„invalid"/„expired"/„authentication" → Refresh triggern via `curl https://api.dropboxapi.com/oauth2/token` mit `grant_type=refresh_token`.
- Nach Refresh: `credentials.json` per Python aktualisieren, **alle Felder erhalten** (`scope`, `uid`, `account_id`, `token_type` nicht überschreiben), nur `access_token`, ggf. `refresh_token`, und `last_refresh` neu schreiben.
- Dann Befehl **nochmal** mit neuem Token ausführen.

**Falls es doch einmal hakt:** Manuell `dbx account` laufen lassen — das triggert beim ersten Aufruf den Refresh und gibt danach Account-Info zurück. Dann das eigentliche `dbx get ...` oder `dbx put ...` nochmal ausführen — funktioniert.

### Bug 2: PKCE-Flow race condition (dbxcli-Pipe-Workaround)

Wenn man `echo code | dbxcli login` pipet, generiert dbxcli pro Aufruf eine neue `code_challenge`, der Code passt dann nicht zur übermittelten Authorization-URL. **Statt:** manuell via `requests` / `curl` und direkt `~/.config/<tool>/credentials.json` setzen. Lehre: nie Codes in dbxcli pipen, wenn ein eigener code_verifier kontrolliert wird.

## OAuth-App-Daten — NICHT hier dokumentieren

App Key + Secret werden **ausschließlich lokal** in `~/.config/dbxcli/credentials.json` und im Wrapper-Skript `~/.local/bin/dbx` gehalten (aus dem Public-Skill entfernt 2026-09-01 nach GitGuardian-Alert, siehe Lessons Learned unten). Für die Rekonstruktion des Setups siehe `dotfiles/local/bin/dbx` oder das private Setup-Notizbuch — **nicht in diesem Skill**.

Account & Email: siehe private Setup-Doku (nicht in Public-Skill).

## Pfade (lokal, nicht im Repo)

```
~/.config/dbxcli/credentials.json   # {access_token, refresh_token, expires_at, ...}
~/.local/bin/dbx                     # Wrapper-Skript
~/.local/bin/dbxcli                  # Original-CLI
```

Dropbox-Pfad: `/Zuhause/Obsidian/Tagebuch/<jahr>.md` (immer diese Schreibweise — `Zuhause`, nicht `Zuhaume`).

Konkrete App-Key / App-Secret / Account-Email stehen ausschließlich in `credentials.json` und in der Dropbox-Dev-Konsole. Vor jedem Push prüft der `skill-pflege`-Skill (siehe dort: „Pre-Commit-Gate"), dass keine dieser Werte versehentlich im Working-Tree landet.

## Häufige Befehle

```bash
dbx account           # Account-Info, triggert Auto-Refresh
dbx ls /Zuhause/Obsidian/Tagebuch/2026.md -l     # Metadaten
dbx get <remote> <lokal>      # Download
dbx put <lokal> <remote>      # Upload
```

## Lessons Learned (warum das hier steht)

- **01.09.2026 — GitGuardian-Alert.** Ein Dropbox-App-Key + Account-Email waren in einer früheren Version dieser Datei im Working-Tree gelandet und beim Push in die Public-History gerutscht. GitGuardian hat es nach 6 Tagen gescannt und eine Mail geschickt. Fix: Datei sanitisiert, History mit `git filter-branch` umgeschrieben, Force-Push. Der ganze Vorfall steht ausführlich in `skill-pflege/SKILL.md` unter „Pitfall — Datei-Sanierung reicht NICHT".
- **`/Zuhaume` ≠ `/Zuhause`.** Der Dropbox-Web-UI zeigt das Wurzelverzeichnis des Accounts als `/Zuhause/snapshot`-Pfad. Der CLI nutzt nackte `/Zuhause`-Pfade. Der Wrapper normalisiert das, sonst sieht man leere Ordner und denkt, die Datei sei weg.
- **Tailscale ist nicht nötig** für Dropbox — Dropbox hat einen offiziellen Endpoint. Nur sinnvoll, wenn man gleichzeitig andere Server erreichen will.

## Siehe auch

- `skill-pflege/SKILL.md` — Negativ-Liste, Pre-Commit-Gate, Multi-Klone-Sync (warum wir hier keine konkreten Werte schreiben)
- `tagebuch-hanno/SKILL.md` — wo der Datei-Inhalt (Tagebuch) liegt, das per Dropbox synchronisiert wird
- `obsidian-pflege` — allgemeine Obsidian-Konventionen
