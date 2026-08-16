# Hanno / Skills

Persönliche Skills, geteilt zwischen Hermes und Claude Code. Format: ein Verzeichnis pro Skill, jede `SKILL.md` mit YAML-Frontmatter und Body.

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
