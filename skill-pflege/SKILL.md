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

### Pitfall — Datei-Sanierung reicht NICHT, Git-History bleibt giftig

**Verifiziert 2026-09-01 (GitGuardian-Alert):** Ein Dropbox-App-Secret stand in einer Reference-Datei. Der File-Sanitizer hat den Wert aus `tagebuch-hanno/references/dbx-wrapper-recipes.md` entfernt — neuer Commit, neue Rev. Aber der **ursprüngliche Commit** (`37375518fc…`) enthält den Secret weiterhin, und `git log -p` zeigt ihn. GitGuardian und andere Scanner sehen genau das. Die Datei zu säubern ist Pflichterfüllung gegenüber der Sache, **nicht** gegenüber dem Scanner.

**Reihenfolge bei Secret-Funden im Public-Repo:**

1. **Sofort rotieren.** Nicht warten, bis History bereinigt ist. Das Secret ist ab dem Moment des Pushes kompromittiert; das gilt auch, wenn es nur Sekunden öffentlich war. Anbieter-Konsole öffnen, Secret/Token neu generieren, lokal einspielen.
2. **Datei säubern, committen, pushen.** Schließt den GitGuardian-Alert-Spam und verhindert weiteres Leak.
3. **History bereinigen** (separater Schritt, niedrigere Prio, braucht Rücksprache wenn Force-Push):
   - `git filter-repo --path <file> --invert-paths` (überschreibt History für alle Branches/Tags)
   - Oder `bfg-repo-cleaner --delete-files <file>` (schneller bei großen Repos)
   - Anschließend `git reflog expire --expire=now --all && git gc --prune=now --aggressive`
   - **Force-Push** erforderlich (`git push --force-with-lease`), bricht für Klone die History-Konsistenz — daher nur in Rücksprache mit Hanno.
4. **Bei mehreren Klones / Mirrorn** (Marvin-Container + Hetzner + Claude via MCP) nach der Bereinigung alle nachziehen — sonst hat einer den alten Stand.

**Was NICHT hilft:** `git commit --amend` auf den ursprünglichen Commit (klont niemanden erneut), Löschen der Datei im aktuellen Stand ohne History-Rewrite, `git filter-branch` (deprecated seit 2020, in vielen Setups nicht mehr installiert).

**Erkennungsmerkmal für „ist es ein echter Secret":** Der Wert matcht ein bekanntes Format (z. B. `sl.u.…` für Dropbox, `ghp_…` oder `github_pat_…` für GitHub, `sk-…` für OpenAI, `xox[bpoas]-…` für Slack) ODER der Wert ist im Klartext deutlich länger als 32 Zeichen mit alphanumerischem/hybridem Inhalt. Bei Zweifel: `git log -p <file>` einmal komplett durchscrollen, der menschliche Blick erkennt Muster, die ein Grep übersieht.

### Was NICHT in einen Skill gehört (Negativ-Liste, lückenlos)

Konkrete Werte sind tabu — **unabhängig davon, ob der Skill lokal oder für Dokumentationszwecke gedacht ist**. Erlaubt ist nur die *Pfad-/Konzept-Beschreibung*, nie der *Inhalt*:

| Was | Beispiel erlaubt | Beispiel verboten |
|---|---|---|
| OAuth App Key / Client ID | `~/.config/dropbox-cli/app.key` | `6ygumfjv2bz7q45` |
| OAuth App Secret / Client Secret | `~/.config/dropbox-cli/app.secret` (Pfad-Referenz) | `tt50ob17zzyo0k9` |
| Access-Token / Refresh-Token | `siehe ~/.config/dropbox-cli/credentials.json` | `sl.u.AGtSMHSb0PMm…` |
| SSH-Public-Keys (Ed25519-Fingerprint) | `~/.ssh/id_ed25519.pub` (Pfad) | der ganze 68-Zeichen-Key-String |
| Persönliche E-Mails Dritter | `Hanno Reents <…@…>` (Rollenname) | `martin@reents.eu` (Klarname-Email) |
| Telefonnummern Dritter | `+49 …` (Format) | konkrete Nummern wie `+49 170 2409866` |
| Private IP-Adressen / Ports zur Infrastruktur | `Tailscale-100.x.x.x-Range, Outpost-Ports 33222/32226` (Konzept) | konkrete IPs wie `100.120.120.100:33222` |
| Klarnamen externer Personen | `Mutter Andrea` (Rolle) | `Andrea Reents <andrea@…>` |
| Private Buchungs-IDs / Reservierungs-Codes | `siehe Nextcloud Tasks` | `HMS4PCDJX` (Airbnb-Code) |

**Faustregel:** Wenn der Wert nur für *einen* Account gilt oder nur *eine Person* identifiziert, gehört er nicht in einen Skill — egal wie harmlos er wirkt. Skills sind **Wiederverwendungs-Werkzeuge**, nicht Setup-Notizen.

### Pitfall — Pre-Commit-Gate (History-Hygiene)

**Verifiziert 2026-09-01 (zweiter Vorfall):** Nach dem GitGuardian-Alert wurde die Datei gesäubert + gefiltert. Trotzdem hätte der Workflow früher ansetzen können. Standard vor jedem Commit:

```bash
cd /home/hermes/repos/Hanno/Skills
git add -A
git diff --cached | grep -E "^[+]" | grep -iE "secret|token|key|password|credential|client[_-]?id|client[_-]?secret|access_token|refresh_token|api[_-]?key|private[_-]?key|sl\.|ghp_|sk-|xox[bpoas]-|-----BEGIN"
echo "(sollte LEER sein; jeder Treffer = prüfen, ob Wert oder nur Konzept-Wort)"
```

**Bei Treffer:** nicht committen. Erst entweder Wert ersetzen (siehe Negativ-Liste) oder, falls nur ein Konzept-Wort gemeint war, Zeile umformulieren.

**Plus — History-Check nach jedem Filter-Branch / Force-Push:**
```bash
git log --all -p | grep -E "6ygumfjv2bz7q45|tt50ob17zzyo0k9|sl\.u\.|ghp_|sk-|xox-"
echo "(sollte LEER sein — Diff-Text zählt nicht, aber unreachable Objects müssen weg sein)"
git reflog expire --expire=now --expire-unreachable=now --all
git gc --prune=now --aggressive
```

### Pitfall — Drei Klones, eine Quelle

Das Repo liegt in drei Checkouts (Marvin nativ, Hetzner sekundär, Claude via Marketplace). **Alle drei müssen nach Force-Push nachgezogen werden**, sonst hat einer den alten Stand mit Secrets. Workflow:

1. **Push erzwingen** auf `origin/main` (Marvin → GitHub).
2. **Marvin-Container** (`/opt/stacks/hermes/skills-repo`): `git pull --ff-only` ODER warten bis zur nächsten Sync-Tick (20 min).
3. **Hetzner-AI-Node** (`/home/hermes/repos/Hanno/Skills`): `git pull --ff-only`.
4. **Claude via Marketplace**: `/plugin marketplace update` (Claude Code) bzw. Web-Re-Sync.
5. **Verifizieren** — auf jedem Checkout ein `git log -p --grep "secret\|token"` bzw. `git grep <verdächtiger_wert>` und sicherstellen, dass nichts Altes mehr da ist.

**Schnelltest über alle drei Klones in einem Rutsch:**
```bash
for path in /opt/stacks/hermes/skills-repo /home/hermes/repos/Hanno/Skills /home/shared/Skills; do
  if [ -d "$path" ]; then
    echo "=== $path ==="
    cd "$path"
    git log --oneline -3
    git grep "<verdächtiger_wert>" 2>/dev/null || echo "(sauber)"
  fi
done
```

## Neuen Skill anlegen

1. Neues Verzeichnis `<skill-name>/` mit `SKILL.md` (YAML-Frontmatter: `name`, `description` — **≤60 Zeichen, ein Satz, Trigger zuerst, endet mit Punkt**, siehe Begründung unten bei "Wie es bei Hermes ankommt"; Body: kurze vollständige Sätze).
2. Falls der Skill für Claude und Hermes unterschiedliche Werkzeuge/Pfade braucht (z. B. Claude nutzt einen Connector, Hermes einen direkten Dateizugriff), einen Abschnitt `## Zugriffsweg (agentenabhängig)` ergänzen — siehe `tagebuch-hanno` und `obsidian-pflege` als Vorlage.
3. Redaktions-Check (oben) durchführen.
4. Commit + Push. Vorher `git pull --ff-only`, um nicht gegen zwischenzeitliche Änderungen der anderen Seite zu laufen; falls das fehlschlägt, erst mergen/rebasen, dann pushen.

**Patch-Tool-Falle bei deutscher Markdown:** Wenn du einen Skill (oder eine andere deutsche Markdown-Datei) per `patch` mit `old_string`/`new_string` editierst, müssen die deutschen Anführungszeichen (`„"`, U+201E/U+201C) im `old_string` **Byte für Byte** mit der Datei übereinstimmen. ASCII `"` matched nicht, auch wenn es „gleich aussieht". Bei Fehlschlag (`Could not find a match for old_string`): die Originalstelle mit `read_file` neu laden, das Unicode-Zeichen 1:1 aus der Datei kopieren, nicht aus dem Gedächtnis. Diese Falle gilt für jeden Skill, der deutsche Texte enthält, nicht nur für Tagebucheinträge.

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
- **Hetzner-AI-Node ist NICHT Marvin.** Wenn der Agent direkt auf Hetzner arbeitet (terminal-backend ssh, user=hermes), ist **nur** `/home/hermes/repos/Hanno/Skills/` der Repo-Checkout. Der in der Doku zuerst genannte Marvin-Pfad `/opt/data/external-skills/hanno-skills/` existiert hier nicht. Schneller Sanity-Check zu Beginn: `ls /home/hermes/repos/Hanno/Skills/<skill-name>/SKILL.md` — wenn das die Datei zeigt, ist alles normal und man kann direkt `git status && git pull --ff-only` machen. Wenn nicht: `find /home/hermes /home/shared -maxdepth 8 -type d -name "<skill-name>" 2>/dev/null` und erst dann die Repo-Wurzel identifizieren.

**Beschreibungslänge:** Hermes' natives `skill_manage`-Tool validiert `description` auf ≤60 Zeichen, ein Satz, Trigger zuerst, endet mit Punkt (System-Prompt-Budget). Alle Skills hier halten sich daran, damit Hermes sie nativ bearbeiten kann, ohne auf die Validierung zu laufen.

**Bekannte Falle — `skill_manage` NICHT für diese fünf Skills nutzen (Stand 2026-08-16):** `skill_manage(action='delete', name=...)` meldet für diese externen Skills "success", löscht aber nichts — stattdessen können native Duplikate an zwei Orten neu entstehen: `/opt/data/skills/` im Marvin-Container UND `/home/hermes/.hermes/skills/` auf Hetzner (Zwei-Wege-Sync zwischen beiden, siehe `tools.environments.file_sync` in den Hermes-Logs). Mehrfach beobachtet: Löschung auf einer Seite hält nicht, wenn die andere Seite noch eine Kopie hat — beide Seiten müssen gleichzeitig bereinigt werden. Für die fünf Skills hier: Änderungen **ausschließlich** über direkte Datei-Edits im externen Pfad + `git push` vornehmen, nicht über `skill_manage create/delete/patch`. Falls `skill_manage` bei einem dieser Namen doch mal anspringt (z. B. weil ein Auto-Fix-Versuch reinrutscht), prüfen ob `/opt/data/skills/<name>` (Marvin) und `/home/hermes/.hermes/skills/<name>` (Hetzner) neu aufgetaucht sind, und beide löschen.

**Erledigt (2026-08-16):** `config.yaml` auf Marvin hatte zusätzlich einen fehlerhaften Wert `terminal.cwd: /home/ubuntu` (SSH-User ist aber `hermes`, Home `/home/hermes`) — live als Fehler in den Logs sichtbar, mitgefixt auf `/home/hermes`.

## Wie Claude selbst schreibend zugreift

Zwei Wege, je nach Oberfläche:

- **Claude Code (Bash-Sitzungen):** Entweder `git`/`gh` (Clone + Commit + Push, sofern die Maschine einen eingeloggten `gh`-Account hat) oder direkt über die GitHub-Contents-API (`gh api repos/HannoRE/Skills/contents/<pfad>`, GET für sha+Inhalt, PUT für Änderungen) — letzteres ohne lokalen Clone, nützlich weil das Scratchpad-Verzeichnis sitzungsgebunden ist und in einer neuen Session nicht mehr existiert. Auf der Haupt-Claude-Maschine läuft das über einen breiten OAuth-Token (voller `repo`-Scope über alle Repos) — bewusst so belassen, da diese Maschine nur von Hanno per Chat gesteuert wird.
- **claude.ai Web:** Seit 2026-08-16 über den `github`-MCP-Server im LiteLLM-Gateway (`api.githubcopilot.com/mcp`, Bearer-Token = derselbe fine-grained PAT wie bei Hermes, beschränkt auf `HannoRE/Skills`), freigegeben für das "Public MCP Team". Damit kann Claude jetzt auch aus claude.ai Web heraus Dateien in diesem Repo lesen und schreiben, nicht mehr nur lesend über den Marketplace-Sync. Der Server-Zugriff gilt pro Session ab dem Zeitpunkt der Team-Freigabe — eine zum Zeitpunkt der Freigabe bereits laufende Session sieht die neuen Tools erst in einer neuen Session.
