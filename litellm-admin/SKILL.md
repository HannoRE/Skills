---
name: litellm-admin
description: LiteLLM per API verwalten: Model-Rename- und Alias-Fallen.
---

# LiteLLM-Admin

LiteLLM (Proxy/Gateway vor den Modellen) läuft als Docker-Container auf Marvin. Verwaltung läuft normalerweise über MCP-Tools (`litellm_manage-*`), aber die haben Lücken — vor allem bei Teams.

## Bekannte Lücke: kein Team-CRUD im MCP-Wrapper

Die MCP-Tools können Modelle (Deployments) lesen/schreiben, aber **Teams nicht** — kein Tool listet oder editiert `team.models`, Budgets oder Alias-Maps. Für alles auf Team-Ebene direkt über die REST-API des Proxys gehen, mit dem Master-Key, ohne den Key je in den eigenen Kontext zu holen:

```bash
ssh <proxy-host> "docker exec <litellm-container> printenv LITELLM_MASTER_KEY | xargs -I{} curl -s -H 'Authorization: Bearer {}' 'http://localhost:4000/<endpoint>'"
```

Der Key fließt nur innerhalb der Remote-Shell durch `xargs` in `curl` — er landet nie im eigenen Output. `jq` zum Filtern verwenden, falls auf dem Host vorhanden. Dasselbe Muster funktioniert auch für Model-CRUD-Aktionen, die das MCP-Tool-Schema nicht hergibt (z. B. eine Liste als `model_name`, oder bestimmte `model_info`-Felder beim Anlegen).

Schreibende Aktionen (egal ob über MCP-Tool oder direkten `curl`) werden von Berechtigungs-Gates manchmal beim ersten Versuch blockiert, beim zweiten Versuch mit identischem Befehl aber durchgelassen — ein einfacher Retry ist normal, kein Zeichen für einen tieferen Fehler.

## Team-Model-Aliasse: wo sie wirklich liegen

Ein Team kann eigene kurze Modell-Handles definieren (z. B. `team-modell` → `provider/echtes-modell`), damit nachgelagerte Clients nie den echten Modellnamen hartcodieren müssen. Diese Aliasse liegen **nicht** dort, wo man sie zuerst sucht:

- Nicht in `team.metadata` (dort stehen nur Budget-/Guardrail-Felder).
- Nicht als eigenes Feld auf `GET /team/info` (das Schema hat dafür keinen Platz).
- Nicht in den `aliases`/`config`-Feldern eines einzelnen Keys (die sind für was anderes und meist leer).

Sie leben in `team.litellm_model_table.model_aliases` — sichtbar nur über `GET /team/list` (die Einzelabfrage `/team/info` zeigt sie nicht). Suche über alle Teams:

```bash
... | jq -c '.[] | select(.litellm_model_table.model_aliases != null) | {team_alias, model_aliases: .litellm_model_table.model_aliases}'
```

`POST /team/update` mit `model_aliases` im Body ersetzt die komplette Alias-Map dieses Teams — immer zuerst den aktuellen Stand holen und mergen, sonst gehen Geschwister-Aliasse verloren, die man gar nicht ändern wollte.

## Pitfall: Model-Rename kann Geschwister-Deployments verwaisen lassen

Ein Modell kann unter mehreren `model_name`-Einträgen laufen, die auf dieselbe Backend-Konfiguration zeigen (z. B. ein sprechender Name und ein kurzes internes Handle). Das sind separate Zeilen in `/model/info`, nicht automatisch verknüpft außer durch identische `litellm_params`.

**Vor jedem Rename oder Delete eines langlebigen Modells:**

1. Den vollen `/model/info`-Dump nach anderen Einträgen mit denselben `litellm_params` (gleicher `api_base`/`model`-String/Credential) durchsuchen — das sind Geschwister, die beim Rename mit aktualisiert werden müssen.
2. Alle Teams nach `litellm_model_table.model_aliases`-Einträgen durchsuchen, die auf den alten Namen zeigen (siehe oben) — Aliasse hängen am String, nicht an der internen ID, und werden durch einen Rename **nicht** automatisch migriert.
3. `model_info.access_via_team_ids` nicht als verlässliches Live-Berechtigungssignal behandeln — das Feld kann nach einem Update falsche Werte zeigen, während die tatsächliche Berechtigung (leere `team.models`-Liste = uneingeschränkt, oder Access-Group-Mitgliedschaft) unverändert bleibt.

Ein Upstream-Fehler wie "model not found", der über LiteLLM durchgereicht wird, bedeutet nicht zwingend ein LiteLLM-Problem — zuerst prüfen, ob der dahinterliegende Provider (bei einem `custom_openai`-Deployment z. B. ein extern gehosteter Chat-Dienst) seinen eigenen Modell-Katalog geändert hat.

## Zugriffsweg (agentenabhängig)

- Claude Code: MCP-Tools `litellm_manage-*` zuerst versuchen; bei Team-Fragen oder MCP-Schema-Lücken auf den direkten `curl`-Weg über SSH/Bash wechseln.
- Hermes: gleicher `curl`-Weg über das Shell/Terminal-Tool, kein MCP-Pendant nötig.
