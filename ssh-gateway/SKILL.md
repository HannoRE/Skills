---
name: ssh-gateway
description: SSH-Zugangslogik zu Hannos Heim-Infrastruktur. Marvin, Minsky, Merlin, Bonfire, mercury und der neue Gateway.
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

## Empfehlungen

- SSH-CA als zentrale Identität statt manuell verteilte authorized_keys
- ProxyJump auf dem neuen Gateway als Default-Einstiegspunkt
- AuthorizedKeysCommand auf Marvin als Mittelweg, falls CA zu invasiv ist
- SSH-CA-Konzept in `Obsidian/Referenz/SSH-CA-Konzept.md` (in Arbeit)

## Merksätze

- Niemals AI-Node direkt SSH in Heimnetz
- Keys auf Gateway zentralisieren, nicht auf jedem Host
- Bei Tests: Tailscale-Status prüfen, dann Marvin, dann Zielhost
