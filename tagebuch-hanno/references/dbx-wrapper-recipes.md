---
name: dbx-wrapper-recipes
description: Dropbox von Headless-Linux aus nutzen — Setup-Skizze für dbxcli + OAuth-Wrapper, sanitized.
---

# Dropbox-CLI: Headless-Setup-Skizze (sanitized)

Kurzfassung für den nächsten Tag, an dem Dropbox von einem Server aus gebraucht wird. Ausführliche Werte, App-Key, Token-Dateien und Pfade zur persönlichen Config sind **lokal** — gehören nicht in ein öffentliches Skill-Repo.

## Was es gibt

Zwei Bausteine:

- **`dbxcli`** — der offizielle Dropbox-CLI. Liest Token aus einer lokalen JSON-Datei (`~/.config/dbxcli/credentials.json`) und refresht es alle 4 h automatisch. Quellcode unter <https://github.com/dropbox/dbxcli>.
- **eigener Wrapper** (`~/.local/bin/dbx`) — kompensiert ein häufiges `dbxcli`-Problem: Pfade mit Sonderzeichen und Umlauten werden anders behandelt als im offiziellen Client. Außerdem erzwingt er den Storage-Root `/Zuhause` (nicht `/Zuhause/snapshot` wie in der Dropbox-Web-UI).

Beide sind lokal installiert, nicht in diesem Repo.

## Wie OAuth läuft (4 Schritte, generisch)

1. **App anlegen** auf `https://www.dropbox.com/developers/apps` — „Scoped access", „App folder" oder „Full Dropbox". Permissions: `files.content.read/write`, `files.metadata.read/write`, `account_info.read`.
2. **Redirect-URI eintragen** (in den App-Settings unter OAuth 2): z. B. `http://127.0.0.1:8484/callback`. Ohne genau diesen Eintrag kommt `Invalid redirect_uri`.
3. **PKCE-Authorization-URL öffnen** mit `client_id`, `redirect_uri`, `scope`, `code_challenge` (SHA256 eines frischen `code_verifier`), `code_challenge_method=S256`, `token_access_type=offline`.
4. **Code aus Redirect-URL abgreifen** (`?code=…`), mit Code + Verifier gegen `https://api.dropboxapi.com/oauth2/token` tauschen → liefert `access_token` (4 h gültig) **und** `refresh_token` (4 Jahre gültig bei `offline`).

Falls die Redirect-URI nicht in der App konfiguriert werden kann: als Workaround in der Dev-Konsole unter „Generated access token" einen kurzen Token generieren (4 h gültig) und in `credentials.json` eintragen — sauberer OAuth geht danach trotzdem.

## Pfade (lokal, nicht im Repo)

```
~/.config/dbxcli/credentials.json   # {access_token, refresh_token, expires_at, ...}
~/.local/bin/dbx                     # Wrapper-Skript
~/.local/bin/dbxcli                  # Original-CLI
```

Konkrete App-Key / App-Secret / Account-Email stehen ausschließlich in `credentials.json` und in der Dropbox-Dev-Konsole. Vor jedem Push prüft der `skill-pflege`-Skill (siehe dort: „Pre-Commit-Gate"), dass keine dieser Werte versehentlich im Working-Tree landet.

## Lessons Learned (warum das hier steht)

- **01.09.2026 — GitGuardian-Alert.** Ein Dropbox-App-Key + Account-Email waren in einer früheren Version dieser Datei im Working-Tree gelandet und beim Push in die Public-History gerutscht. GitGuardian hat es nach 6 Tagen gescannt und eine Mail geschickt. Fix: Datei sanitisiert, History mit `git filter-branch` umgeschrieben, Force-Push. Der ganze Vorfall steht ausführlich in `skill-pflege/SKILL.md` unter „Pitfall — Datei-Sanierung reicht NICHT".
- **`/Zuhaume` ≠ `/Zuhause`.** Der Dropbox-Web-UI zeigt das Wurzelverzeichnis des Accounts als `/Zuhause/snapshot`-Pfad. Der CLI nutzt nackte `/Zuhause`-Pfade. Der Wrapper normalisiert das, sonst sieht man leere Ordner und denkt, die Datei sei weg.
- **Tailscale ist nicht nötig** für Dropbox — Dropbox hat einen offiziellen Endpoint. Nur sinnvoll, wenn man gleichzeitig andere Server erreichen will.

## Siehe auch

- `skill-pflege/SKILL.md` — Negativ-Liste, Pre-Commit-Gate, Multi-Klone-Sync (warum wir hier keine konkreten Werte schreiben)
- `tagebuch-hanno/SKILL.md` — wo der Datei-Inhalt (Tagebuch) liegt, das per Dropbox synchronisiert wird