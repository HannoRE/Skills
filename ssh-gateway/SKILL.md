---
name: ssh-gateway
description: SSH zu Hannos Heimserver: immer über Gateway, nie direkt.
---

# SSH – Gateway-Logik

Hannos Heimserver liegen in einem privaten Netz. Direkter SSH-Zugriff von AI-Nodes ist nicht vorgesehen. Immer über Tailscale oder über die VPN-/Gateway-Hops, die der jeweilige Server erwartet.

## Server-Inventar

- Marvin: Hauptserver, Docker, Graylog, Nextcloud, Authentik, Jumphost
- Minsky: GPU-Host, Outpost-Container
- Merlin: Backup-Ziel, Wake-on-LAN für Borg
- Bonfire: ESXi-Lab, WoL
- Archimedes: USV für Marvin
- mercury: Cloud-Gateway, aktuell gestoppt
- Oracle: Oracle-Cloud-Node (Ubuntu 24.04, Tailscale-tagged)
- kali: Tailscale-Peer, nur online bei Bedarf

## Outpost-Wrapper (Custom-Ports!)

Marvin und Minsky sind **nicht** auf Standard-Port 22 erreichbar — sie hängen hinter **Outpost-Wrapper-Hosts** mit eigenen Ports:

| SSH-Alias | Ziel | Host (Tailscale-IP) | Port | User |
|---|---|---|---|---|
| `outpost-marvin` | Marvin | 100.120.120.100 | **33222** | hanno |
| `outpost` | Minsky | 100.120.110.100 | **32226** | hanno |
| `oracle` | Oracle | 100.120.50.120 | 22 | hanno |

Die Aliase stehen in `~/.ssh/config` auf dem AI-Node. **Immer `ssh <alias>` benutzen, nicht die IP direkt** — sonst landet man auf einem geschlossenen Port und wähnt sich fälschlich „ausgesperrt".

## Vor jedem Test: Config lesen, dann entscheiden

Bevor „kein SSH-Zugriff" gemeldet wird:
1. `cat ~/.ssh/config` — gibt es Aliase mit Custom-Ports?
2. `tailscale status` — ist der Zielhost überhaupt online?
3. Erst dann mit `ssh -v <alias>` testen

**Anti-Pitfall:** Wer direkt auf `host 22` der Tailscale-IP pingt/ssht und nichts zurückkommt, schließt fälschlich auf „Firewall blockt" — dabei läuft SSH auf einem **Outpost-Port**. Hanno hat das am 30.08.2026 einmal korrigieren müssen.

**Anti-Pitfall (Wiederholung am 01.09.2026):** Der erste Reflex beim „kein SSH-Zugriff"-Verdacht ist `~/.ssh/known_hosts` oder `~/.ssh/authorized_keys` zu prüfen. Beide sind **irrelevant** für die Frage „warum komme ich nicht auf den Wrapper-Host". Die einzig maßgebliche Datei ist `~/.ssh/config` — dort stehen die Aliasse mit ihren Custom-Ports, und **ohne sie** landet man auf Standard-Port 22, der auf Outpost-Wrappern geschlossen ist. Reihenfolge beim SSH-Probing strikt:

1. `cat ~/.ssh/config` — Aliasse mit Ports lesen (1 Sekunde, löst die Frage meistens sofort)
2. `tailscale status` — ist der Zielhost online?
3. `ssh -v <alias>` mit dem Alias-Namen (nicht der IP!) — verbose-Output zeigt, welcher Port tatsächlich angesprochen wird
4. **Erst dann**, wenn alle drei Punkte ohne Ergebnis bleiben: Firewall/Netzwerk prädikatisieren

Wer Schritt 1 überspringt, brennt 5–10 Minuten Debug-Zeit für nichts — ist im konkreten Fall zweimal hintereinander passiert (30.08. und 01.09.2026).

## Empfehlungen

- SSH-CA als zentrale Identität statt manuell verteilte authorized_keys
- ProxyJump auf dem neuen Gateway als Default-Einstiegspunkt
- AuthorizedKeysCommand auf Marvin als Mittelweg, falls CA zu invasiv ist
- SSH-CA-Konzept in `Obsidian/Referenz/SSH-CA-Konzept.md` (in Arbeit)

## Merksätze

- Niemals AI-Node direkt SSH in Heimnetz
- Keys auf Gateway zentralisieren, nicht auf jedem Host
- Bei Tests: Tailscale-Status prüfen, dann Marvin, dann Zielhost

## Zugriffsweg (agentenabhängig)

- Claude Code (Bash-Sitzungen): hat SSH-Zugriff, diese Regeln gelten direkt.
- claude.ai Web: kein Bash, kein SSH — dieser Skill ist dort nicht ausführbar. Bei SSH-Bedarf aus Web-Sitzungen an Claude Code oder Hermes verweisen.
- Hermes: hat Shell-Zugriff, diese Regeln gelten direkt.
