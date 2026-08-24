# dbx-Wrapper Recipes

Der `dbx`-Wrapper (`~/.local/bin/dbx`) ist ein Auto-Refresh-Wrapper für `dbxcli` mit Dropbox-OAuth. Diese Notiz fasst Bugs, Fixes und Gotchas zusammen, damit eine zukünftige Session das Rad nicht neu erfinden muss.

## Mechanik

- Liest `~/.config/dbxcli/credentials.json` (enthält `access_token` + `refresh_token` + `client_id` + `client_secret`).
- Access-Token läuft nach 4 Std ab, Refresh-Token hält ~4 Jahre.
- App Key + Secret sind hardcoded im Wrapper — der Wrapper refresht selbst, nicht via dbxcli.

## Bekannte Bugs / Fixes

### Bug 1: Stille Outputs, keine Schreibvorgänge (gefixt mit Commit, falls noch nicht gemergt)

**Symptom:** `dbx get …` und `dbx put …` geben **keinen Fehler** aus und schreiben/laden **nichts** — der Output ist einfach leer. Exit-Code 0 oder ohne klares Signal.

**Ursache:** Der Wrapper hatte eine frühe Version mit `set -e` und einem fehlerhaften Refresh-Pfad. Wenn der Access-Token abläuft, schlägt `dbxcli` mit „expired_access_token" fehl, der Refresh-Path aber wird durch `set -e` vorzeitig abgebrochen, **bevor** er den eigentlichen Befehl nochmal probiert.

**Workaround (sofort):**
1. Manuell `dbx account` laufen lassen — das triggert beim ersten Aufruf den Refresh und gibt danach Account-Info zurück.
2. Dann das eigentliche `dbx get ...` oder `dbx put ...` nochmal ausführen — funktioniert.

**Langfristig:** Wenn du den Wrapper neu schreibst oder patchst:
- KEIN `set -e` im Wrapper
- Refresh-Pfad triggert **immer**, nicht nur bei Auth-Error (proaktiver Refresh)
- Bei JSON-Update `last_refresh` per `date -Iseconds` in UTC setzen
- Alle Felder der `credentials.json` erhalten, nicht nur `access_token`/`refresh_token` überschreiben (sonst gehen `scope`, `uid`, `account_id`, `token_type` verloren)

### Bug 2: PKCE-Flow race condition (dbxcli-Pipe-Workaround)

Wenn man `echo code | dbxcli login` pipet, generiert dbxcli pro Aufruf eine neue `code_challenge`, der Code passt dann nicht zur übermittelten Authorization-URL. **Statt:** manuell via `requests` / `curl` und direkt `~/.config/<tool>/credentials.json` setzen. Lehre: nie Codes in dbxcli pipen, wenn ein eigener code_verifier kontrolliert wird.

## OAuth-App-Daten (lokalisiert in credentials.json + wrapper)

- App Key: `6ygumfjv2bz7q45`
- App Secret: `tt50ob17zzyo0k9`
- Account: Hanno Reents <hanno@reents.tv>, Pro

## Häufige Befehle

```bash
dbx account           # Account-Info, triggert Auto-Refresh
dbx ls /Zuhaume/Obsidian/Tagebuch/2026.md -l     # Metadaten
dbx get <remote> <lokal>      # Download
dbx put <lokal> <remote>      # Upload
```

## Pfade

- Dropbox: `/Zuhaume/Obsidian/Tagebuch/<jahr>.md` (der „Zuhaume"-Tippfehler ist persistent — dbxcli hat ihn einmal so angelegt; funktioniert, einfach konsistent verwenden)
- Lokales Repo mit dem Wrapper-Skript: `~/.local/bin/dbx`

## Siehe auch

- Skill `nextcloud-aufgaben` für Task-Anlage
- Skill `obsidian-pflege` für Obsidian-Konventionen
