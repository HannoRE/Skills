# Hanno / Skills

Persönliche Skills, geteilt zwischen Hermes und Claude Code. Format: ein Verzeichnis pro Skill, jede `SKILL.md` mit YAML-Frontmatter und Body.

## ⚠️ Dieses Repo ist öffentlich — passe auf, was du reinpushst

**Stand 2026-09-01:** GitGuardian hat einen Dropbox-App-Key in einem früheren Commit dieses Repos gefunden, weil er versehentlich in einer Reference-Datei gelandet war. Der Fix war aufwändig (Datei sanitizen, History mit `git filter-branch` umschreiben, Force-Push). Damit das nicht nochmal passiert:

- **Skill-Inhalte beschreiben Werkzeuge, nicht Setup.** Konzept erlaubt, konkrete Werte tabu.
- **Was NICHT in einen Skill gehört:** App-Key, App-Secret, Access-/Refresh-Token, SSH-Public-Key, persönliche E-Mails, Telefonnummern, konkrete IP-Adressen + Ports, Klarnamen externer Personen, Buchungs-Codes (Airbnb etc.). Ausführliche Negativ-Liste mit Beispielen in `skill-pflege/SKILL.md`.
- **Vor jedem Commit läuft ein Grep-Gate** (siehe `skill-pflege/SKILL.md` → „Pre-Commit-Gate"): wenn eine hinzugefügte Zeile wie ein Secret aussieht, wird sie gefangen — bevor sie in der Public-History landet.
- **Wenn du unsicher bist, ob ein Inhalt öffentlich werden darf:** lieber in `~/Notes/...` ablegen (lokal, privat) als im Repo. Skills bleiben so generisch, dass sie für jeden Nutzer funktionieren.
- **Wenn ein Secret doch mal leakt:** erst rotieren (Anbieter-Konsole), dann Datei säubern + pushen, dann History bereinigen. Die volle Reihenfolge steht in `skill-pflege/SKILL.md` unter „Pitfall — Datei-Sanierung reicht NICHT".

Drei Klones hängen an diesem Repo (Marvin nativ, Hetzner sekundär, Claude via Marketplace) — bei Force-Push müssen alle drei nachgezogen werden. Auch das ist in `skill-pflege/SKILL.md` dokumentiert.

## Konventionen

- Frontmatter: `name` (kebab-case), `description` (eine Zeile, Trigger-Bedingung).
- Body: kurze, vollständige Sätze, keine Marketing-Sprache.
- Niemals vorhandene Inhalte überschreiben, nur patchen.
- Vor jedem Schreibvorgang die Zieldatei lesen.

## Geteilte Skills

- `tagebuch-hanno` – Tagebuch-Workflow
- `obsidian-pflege` – Obsidian-Konventionen
- `ssh-gateway` – SSH-Zugangslogik
- `mail-umgang` – Mail-Antwortmuster

## Verwendung

Hermes: `skill_view(name='...')` aus `~/./skills/`.
Claude Code: `~/.claude/skills/` als Symlink auf dieses Repo.

## Sync

Hanno klont das Repo und legt einen Symlink nach `~/./skills/` für Hermes und nach `~/.claude/skills/` für Claude Code. Bei Änderungen `git pull` im Repo.
