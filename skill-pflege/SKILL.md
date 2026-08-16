---
name: skill-pflege
description: Skills in diesem Repo sicher anlegen, ändern und pflegen.
---

# Skill-Pflege

Dieses Repo (`HannoRE/Skills` auf GitHub, alleinige Quelle — kein Gitea-Spiegel mehr) ist **öffentlich**. Beide Agenten — Claude und Hermes — lesen und schreiben dieselbe Skill-Liste, aber über unterschiedliche Mechanismen. Dieser Skill hält beide Seiten synchron.

**Nur für geteilte Skills.** Beide Agenten dürfen zusätzlich eigene, rein agentenspezifische Skills in ihrem jeweiligen nativen Bereich behalten (z. B. Hermes' eigene devops/gaming/creative-Skills unter `~/.hermes/skills/`, oder Claude-spezifische Skills aus anderen Marketplaces) — die müssen nicht hierher. Hierher gehört nur, was beide potenziell brauchen oder verstehen können.

**Mandat:** Claude nutzt und passt die Skills hier eigenständig an. Wenn ein neuer wiederkehrender Bedarf auftaucht, legt Claude ohne Rückfrage einen neuen Skill an — solange der Redaktions-Check (unten) eingehalten wird und, falls relevant, der Zugriffsweg für Hermes mitgedacht/übersetzt wird. Bei strukturellen Änderungen (z. B. neue Mechanismen, neue externe Systeme einbinden) gilt weiterhin: Rücksprache mit Hanno.

## Vor jedem Push: Redaktions-Check

Weil das Repo öffentlich ist, vor jedem Commit prüfen:

- Keine echten Namen, Firmen oder Beziehungsdetails Dritter (siehe `mail-umgang` als Beispiel für generische Rollenbezeichnungen statt Klarnamen).
- Keine IP-Adressen, Hostnamen mit Zuordnung zu echter Infrastruktur, Zugangsdaten oder interne Topologie-Details (siehe `ssh-gateway` als Beispiel — Servernamen als Codenamen sind ok, IPs/Hardware-Specs nicht).
- Keine Secrets/Tokens in Klartext, auch nicht in Beispielen.
- Im Zweifel: Detail durch generische Formulierung ersetzen, nicht ganz weglassen — der Skill muss weiter funktional bleiben.

## Neuen Skill anlegen

1. Neues Verzeichnis `<skill-name>/` mit `SKILL.md` (YAML-Frontmatter: `name`, `description` — **≤60 Zeichen, ein Satz, Trigger zuerst, endet mit Punkt**, siehe Begründung unten bei "Wie es bei Hermes ankommt"; Body: kurze vollständige Sätze).
2. Falls der Skill für Claude und Hermes unterschiedliche Werkzeuge/Pfade braucht (z. B. Claude nutzt einen Connector, Hermes einen direkten Dateizugriff), einen Abschnitt `## Zugriffsweg (agentenabhängig)` ergänzen — siehe `tagebuch-hanno` und `obsidian-pflege` als Vorlage.
3. Redaktions-Check (oben) durchführen.
4. Commit + Push. Vorher `git pull --ff-only`, um nicht gegen zwischenzeitliche Änderungen der anderen Seite zu laufen; falls das fehlschlägt, erst mergen/rebasen, dann pushen.

## Wie es bei Claude ankommt

Automatisch, kein Zusatzschritt: `.claude-plugin/marketplace.json` referenziert das gesamte Repo mit `"skills": "."`. Claude Code (`/plugin marketplace update`) und claude.ai (Customize → Plugins → Sync) scannen den Repo-Root nach `<name>/SKILL.md`-Verzeichnissen und finden neue Skills selbständig.

Nur bei strukturellen Änderungen (z. B. ein komplett neues, separates Plugin statt eines weiteren Skill-Ordners) muss `.claude-plugin/marketplace.json` selbst angepasst werden.

## Wie es bei Hermes ankommt

**Wichtig — Namensverwirrung:** Es gibt zwei verschiedene Dinge, die "Hermes" heißen. Die kanonische Instanz ist der `nousresearch/hermes-agent`-Container im Docker-Stack `hermes` auf **Marvin** (`/opt/stacks/hermes/`). Der arbeitet aber (`terminal.backend: ssh`) auf einer separaten Maschine, dem Server mit SSH-Alias `hetzner` (User `hermes` dort) — das ist nur die Werkbank, nicht der native Skill-Speicher. Beide sind involviert, aus unterschiedlichen Gründen:

- **Marvin (`/opt/stacks/hermes/skills-repo`)** — Git-Checkout, per Docker-Volume in den Container gemountet (`/opt/data/external-skills/hanno-skills`), eingetragen in `config.yaml` unter `skills.external_dirs`. **Das ist der native Weg**: Hermes' eigene Tools (`skill_view`, `skill_manage`, `hermes skills list`) lesen/schreiben hier direkt. Bei Namensgleichheit mit einem selbst angelegten Skill in `~/.hermes/skills/` (nativer Bereich) gewinnt laut Hermes-Doku der native — deshalb dürfen für diese fünf Skills dort keine gleichnamigen Duplikate existieren (Stand 2026-08-16: bereinigt).
- **Hetzner (`/home/hermes/repos/Hanno/Skills`)** — separater Checkout, erreichbar über Hermes' `terminal`-Tool (SSH-Backend). Nützlich für manuelle/Shell-Inspektion, aber **nicht** die Quelle für `skill_view`/`skill_manage`. Kein automatischer Sync (siehe unten) — Hermes pullt hier bei Bedarf manuell.

Beide Checkouts haben Push-Zugriff über denselben Fine-grained GitHub-Token (beschränkt auf `HannoRE/Skills`, Contents Read & Write).

**Sync-Strategie (Stand 2026-08-16):**

- **Marvin (nativer Pfad, muss aktuell sein):** systemd-User-Timer `hanno-skills-sync.{service,timer}` pullt alle 20 Minuten automatisch (Lingering aktiv, überlebt Reboots). Wenn Hermes hier nativ einen Skill ändert, direkt danach `git pull --ff-only && git push` — nicht auf den nächsten Timer-Lauf warten.
- **Hetzner (sekundär, nur bei Bedarf genutzt):** kein Timer mehr (entfernt am 2026-08-16 — zu teuer für einen selten genutzten Pfad). Vor jeder Nutzung dort erst `git pull --ff-only`, nach jeder Änderung `git push`.

**Beschreibungslänge:** Hermes' natives `skill_manage`-Tool validiert `description` auf ≤60 Zeichen, ein Satz, Trigger zuerst, endet mit Punkt (System-Prompt-Budget). Alle Skills hier halten sich daran, damit Hermes sie nativ bearbeiten kann, ohne auf die Validierung zu laufen.

**Bekannte Falle — `skill_manage` NICHT für diese fünf Skills nutzen (Stand 2026-08-16):** `skill_manage(action='delete', name=...)` meldet für diese externen Skills "success", löscht aber nichts — stattdessen können native Duplikate an zwei Orten neu entstehen: `/opt/data/skills/` im Marvin-Container UND `/home/hermes/.hermes/skills/` auf Hetzner (Zwei-Wege-Sync zwischen beiden, siehe `tools.environments.file_sync` in den Hermes-Logs). Mehrfach beobachtet: Löschung auf einer Seite hält nicht, wenn die andere Seite noch eine Kopie hat — beide Seiten müssen gleichzeitig bereinigt werden. Für die fünf Skills hier: Änderungen **ausschließlich** über direkte Datei-Edits im externen Pfad + `git push` vornehmen, nicht über `skill_manage create/delete/patch`. Falls `skill_manage` bei einem dieser Namen doch mal anspringt (z. B. weil ein Auto-Fix-Versuch reinrutscht), prüfen ob `/opt/data/skills/<name>` (Marvin) und `/home/hermes/.hermes/skills/<name>` (Hetzner) neu aufgetaucht sind, und beide löschen.

**Erledigt (2026-08-16):** `config.yaml` auf Marvin hatte zusätzlich einen fehlerhaften Wert `terminal.cwd: /home/ubuntu` (SSH-User ist aber `hermes`, Home `/home/hermes`) — live als Fehler in den Logs sichtbar, mitgefixt auf `/home/hermes`.

## Wie Claude selbst schreibend zugreift

Zwei Wege, je nach Oberfläche:

- **Claude Code (Bash-Sitzungen):** Entweder `git`/`gh` (Clone + Commit + Push, sofern die Maschine einen eingeloggten `gh`-Account hat) oder direkt über die GitHub-Contents-API (`gh api repos/HannoRE/Skills/contents/<pfad>`, GET für sha+Inhalt, PUT für Änderungen) — letzteres ohne lokalen Clone, nützlich weil das Scratchpad-Verzeichnis sitzungsgebunden ist und in einer neuen Session nicht mehr existiert. Auf der Haupt-Claude-Maschine läuft das über einen breiten OAuth-Token (voller `repo`-Scope über alle Repos) — bewusst so belassen, da diese Maschine nur von Hanno per Chat gesteuert wird.
- **claude.ai Web:** Seit 2026-08-16 über den `github`-MCP-Server im LiteLLM-Gateway (`api.githubcopilot.com/mcp`, Bearer-Token = derselbe fine-grained PAT wie bei Hermes, beschränkt auf `HannoRE/Skills`), freigegeben für das "Public MCP Team". Damit kann Claude jetzt auch aus claude.ai Web heraus Dateien in diesem Repo lesen und schreiben, nicht mehr nur lesend über den Marketplace-Sync. Der Server-Zugriff gilt pro Session ab dem Zeitpunkt der Team-Freigabe — eine zum Zeitpunkt der Freigabe bereits laufende Session sieht die neuen Tools erst in einer neuen Session.
