---
name: ssh-gateway
description: "SSH zu Hannos Servern: direkt per Tailscale mit Tresor-Keys."
---

# SSH – Zugriff auf Hannos Server

Hannos Server und Geräte liegen im privaten Tailscale-Netz. Seit 2026-10-20 erreicht jedes Gerät jedes andere **direkt per Tailscale**; es gibt keinen Hop mehr über Marvin. Die SSH-Schlüssel liegen im Bitwarden Secrets Manager, jedes Gerät lädt sie in einen eigenen Agenten. Konkrete IPs, Schlüssel und Ports stehen nur in der SSH-Config auf dem jeweiligen Gerät, nicht hier (öffentliches Repo).

## Server-Inventar

- Marvin: Hauptserver, Docker, Graylog, Nextcloud, Authentik, Jumphost für externe Outposts
- Minsky: GPU-Host, trägt Outpost-Container; meist aus, per Wake-on-LAN oder Home-Assistant-Schalter
- Merlin: Backup-Ziel, Wake-on-LAN für Borg
- Bonfire: ESXi-Lab, Wake-on-LAN
- Mercury: persönlicher Arbeitsplatz und Gateway (Cloud-VM), hier läuft Claude Code
- Oracle: kleine Allzweck-Compute-Node
- Hetzner: Box für autonome Hintergrund-Agenten
- Archimedes: USV für Marvin, kein SSH
- kali: Tailscale-Peer, nur bei Bedarf online

## Wie der Zugriff funktioniert

- **Immer `ssh <alias>`**, nie die IP. Die Aliase stehen in `~/.ssh/config`, die gemeinsame Host-Datei `~/.ssh/tresor-hosts.conf` wird dort per `Include` eingebunden. Jeder Host nennt nur die Public-Key-Datei (`~/.ssh/tresor/<name>.pub`) plus `IdentitiesOnly yes`; den privaten Schlüssel liefert der Agent.
- **Der Agent** ist ein eigener Socket unter `/run/user/<uid>/bws-ssh-agent.socket`, gestartet von den User-Units `ssh-agent-bws.service` und `bws-ssh-load.service` (der Loader lädt alle Schlüssel aus dem Tresor-Projekt). Neue Secrets im Tresor erscheinen erst nach `systemctl --user restart bws-ssh-load.service`.
- **Notroute zu Marvin:** `ssh marvin-notfall` nutzt einen klassischen Datei-Schlüssel ohne Agent, unabhängig von Bitwarden. Nur verwenden, wenn der Tresor oder Agent nicht funktioniert.
- **Outpost-Aliase (`outpost`, `outpost-marvin`) sind Container, nicht die Hosts.** Sie hängen an eigenen Ports (steht in der Config). Marvin und Minsky selbst erreicht man über `marvin` und `minsky`.
- Marvin selbst behält seine eigenen Schlüsseldateien und einen Keychain-Agenten als Rückfallebene.

Details zu Schlüsselliste, Geräten und Tokens: Obsidian-Notiz „SSH-Key-Inventar“. Neuaufbau eines Geräts: Repo `Mercury-Backup-Configs`, README, Abschnitt „SSH-Keys aus dem Tresor“.

## Geräte, die aus sind

Minsky, Desktop, Merlin, Bonfire und die Pis laufen meist nicht. Sie lassen sich per Wake-on-LAN oder dem Schalter in Home Assistant wecken. Minsky braucht etwa vier bis fünf Minuten bis SSH antwortet, der Outpost-Container darauf noch ein bis zwei Minuten länger, auch wenn der Tailscale-Ping schon durchgeht. Nach der Arbeit wieder ausschalten (Minsky verbraucht viel Strom).

## Vor „kein SSH-Zugriff“: in dieser Reihenfolge prüfen

1. `ssh -v <alias>` mit dem **Alias**: zeigt, welcher Port und welcher Schlüssel tatsächlich benutzt werden.
2. `tailscale status`: ist der Zielhost online?
3. Der Agent: existiert der Socket (`ls /run/user/$(id -u)/bws-ssh-agent.socket`), zeigt `ssh-add -l` die Schlüssel, laufen die beiden User-Units?
4. Ein **TCP-Timeout** statt „connection refused“ weist auf eine fehlende Tailscale-ACL hin (typisch bei neuen Geräten außerhalb des Heimnetzes), nicht auf einen fehlenden Schlüssel.
5. Erst wenn alles davon ohne Ergebnis bleibt: Firewall oder Netzwerk vermuten.

**Anti-Pitfalls:**
- Wer die IP direkt benutzt, umgeht die Config und landet auf Standard-Port 22 mit dem falschen Schlüssel oder, bei Outposts, auf einem geschlossenen Port. Daraus nicht auf „Firewall blockt“ schließen.
- `known_hosts` und `authorized_keys` sind für die Frage „warum komme ich nicht auf den Host“ meist irrelevant. Zuerst die Config lesen.
- Private Schlüssel nie ausgeben oder in Logs und Chats stehen lassen. Beim Tresor-Werkzeug `bws` Werte immer mit `--` bzw. `--value=` übergeben: Ein Syntaxfehler gibt sonst den Schlüssel in der Fehlermeldung aus.

## Merksätze

- Direkt per Tailscale, kein Hop über Marvin.
- Alias statt IP, Config vor Vermutung.
- Die Notroute nur bei defektem Tresor.
- Schlüssel liegen im Tresor, nicht auf den Geräten verteilt.

## Zugriffsweg (agentenabhängig)

- Claude Code (Bash-Sitzungen): Mercury, Laptop und Desktop haben den Tresor-Agenten. Die Regeln oben gelten direkt.
- claude.ai Web: kein Bash, kein SSH. Bei SSH-Bedarf an Claude Code verweisen.
- Hermes-Agenten: laut Server-Doku mit eingeschränkter eigener `~/.ssh/config` (nur Outpost-Container und Oracle), ohne den Tresor und ohne Zugriff auf Marvin selbst. Die Aliase und Wege dort kommen aus der Konfiguration des jeweiligen Agenten.
