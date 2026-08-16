---
name: ssh-gateway
description: SSH-Zugangslogik zu Hannos Heim-Infrastruktur. Marvin, Minsky, Merlin, Bonfire, mercury und der neue Gateway.
---

# SSH – Gateway-Logik

Hannos Heimserver liegen im privaten Netz (192.168.2.0/24). Direkter SSH-Zugriff von AI-Nodes ist nicht vorgesehen. Immer über Tailscale oder über die VPN-/Gateway-Hops, die der jeweilige Server erwartet.

## Server-Inventar

- Marvin: Hauptserver, Docker, Graylog, Nextcloud, Authentik, Jumphost, 192.168.2.9
- Minsky: GPU-Host (RTX 3090, RTX 5060 Ti), Outpost-Container, iLO-Warnung läuft
- Merlin: Backup-Ziel, 14 TB, Wake-on-LAN für Borg (Di 01:00)
- Bonfire: ESXi-Lab, 192.168.2.15, WoL
- Archimedes: USV für Marvin
- mercury (Oracle Cloud): A1-Flex, 2 OCPU/12 GB, Public 89.168.112.175, Private 10.0.0.136, AD-1/FD-1, gestoppt

## Empfehlungen

- SSH-CA als zentrale Identität statt manuell verteilte authorized_keys
- ProxyJump auf dem neuen Gateway als Default-Einstiegspunkt
- AuthorizedKeysCommand auf Marvin als Mittelweg, falls CA zu invasiv ist
- SSH-CA-Konzept in `Obsidian/Referenz/SSH-CA-Konzept.md` (in Arbeit)

## Merksätze

- Niemals AI-Node direkt SSH in Heimnetz
- Keys auf Gateway zentralisieren, nicht auf jedem Host
- Bei Tests: Tailscale-Status prüfen, dann Marvin, dann Zielhost
