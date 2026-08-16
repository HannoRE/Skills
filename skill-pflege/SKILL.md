---
name: skill-pflege
description: Wie ein Skill in diesem Repo sicher angelegt, geändert und für Claude und Hermes wirksam gemacht wird. Verwenden bei jeder Änderung an einem Skill hier.
---

# Skill-Pflege

Dieses Repo (`HannoRE/Skills` auf GitHub, gespiegelt nach `Hanno/Skills` auf Gitea) ist **öffentlich**. Beide Agenten — Claude und Hermes — lesen und schreiben dieselbe Skill-Liste, aber über unterschiedliche Mechanismen. Dieser Skill hält beide Seiten synchron.

## Vor jedem Push: Redaktions-Check

Weil das Repo öffentlich ist, vor jedem Commit prüfen:

- Keine echten Namen, Firmen oder Beziehungsdetails Dritter (siehe `mail-umgang` als Beispiel für generische Rollenbezeichnungen statt Klarnamen).
- Keine IP-Adressen, Hostnamen mit Zuordnung zu echter Infrastruktur, Zugangsdaten oder interne Topologie-Details (siehe `ssh-gateway` als Beispiel — Servernamen als Codenamen sind ok, IPs/Hardware-Specs nicht).
- Keine Secrets/Tokens in Klartext, auch nicht in Beispielen.
- Im Zweifel: Detail durch generische Formulierung ersetzen, nicht ganz weglassen — der Skill muss weiter funktional bleiben.

## Neuen Skill anlegen

1. Neues Verzeichnis `<skill-name>/` mit `SKILL.md` (YAML-Frontmatter: `name`, `description`; Body: kurze vollständige Sätze).
2. Falls der Skill für Claude und Hermes unterschiedliche Werkzeuge/Pfade braucht (z. B. Claude nutzt einen Connector, Hermes einen direkten Dateizugriff), einen Abschnitt `## Zugriffsweg (agentenabhängig)` ergänzen — siehe `tagebuch-hanno` und `obsidian-pflege` als Vorlage.
3. Redaktions-Check (oben) durchführen.
4. Commit + Push (`git push origin main`, sowohl auf `github`-Remote als auch `origin`/Gitea-Remote, falls beide konfiguriert sind).

## Wie es bei Claude ankommt

Automatisch, kein Zusatzschritt: `.claude-plugin/marketplace.json` referenziert das gesamte Repo mit `"skills": "."`. Claude Code (`/plugin marketplace update`) und claude.ai (Customize → Plugins → Sync) scannen den Repo-Root nach `<name>/SKILL.md`-Verzeichnissen und finden neue Skills selbständig.

Nur bei strukturellen Änderungen (z. B. ein komplett neues, separates Plugin statt eines weiteren Skill-Ordners) muss `.claude-plugin/marketplace.json` selbst angepasst werden.

## Wie es bei Hermes ankommt

Hermes hat Push-Zugriff auf das Repo (Fine-grained GitHub-Token, beschränkt auf `HannoRE/Skills`, Contents Read & Write, über `gh auth login` auf dem Hetzner-Server eingerichtet).

Der lokale Checkout liegt unter `/home/hermes/repos/Hanno/Skills` und muss nach Änderungen per `git pull` aktualisiert werden.

**Offen (Stand 2026-08-16):** Die eigentliche Einbindung in Hermes' Skill-Hub (`openclaw skills install <pfad> --as <slug> --global`, kopierbasiert, kein Live-Symlink) ist noch nicht eingerichtet — bewusst zurückgestellt. Bis das steht, sind die Skills im lokalen Checkout vorhanden, aber nicht automatisch Teil von Hermes' aktivem Skill-Set. Vor dem Einrichten dieses Schritts: mit Hanno Rücksprache halten (openclaw-Interna sind nicht trivial, siehe Recherche vom 2026-08-16).

## Wie Claude selbst schreibend zugreift

Claude Code (CLI-Sitzungen mit Bash-Zugriff) kann direkt per `git`/`gh` committen und pushen, sofern die Maschine einen eingeloggten `gh`-Account hat. Auf der Haupt-Claude-Maschine ist das aktuell ein breiter OAuth-Token (voller `repo`-Scope über alle Repos, nicht auf `HannoRE/Skills` beschränkt) — bewusst so belassen, da diese Maschine nur von Hanno per Chat gesteuert wird.

claude.ai Web selbst kann Skills **nicht** editieren (nur lesend über den Marketplace-Sync) — Änderungen kommen ausschließlich über eine Claude-Code-Sitzung oder über Hermes.
