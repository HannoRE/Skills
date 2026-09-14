---
name: standort-handys
description: "Use when Hanno's location is needed: check HA sensors first."
---

# Hannos Standort über Home Assistant

Hannos aktueller Standort ist über die HA Companion-App auf seinem Handy ablesbar — die Basis, wenn Karten-/Navigationstools einen Standortkontext brauchen.

## Quelle

- **Primär:** `person.hanno` in Home Assistant (State: home / not_home / Zone)
- **Gerätesource:** `device_tracker.sm_s921u1` = Samsung Galaxy S24 („Hanno S24“), connected per HA Companion-App

## Handy-Sensoren: Akku, Schlaf, Wecker, Schritte

- `..._battery_level` / `..._battery_state` / `..._charger_type` — Akku/Ladung
- `..._geocoded_location` — letzte geocodierte Adresse (Text)
- `..._steps_sensor` — Schritte heute
- `..._next_alarm` — nächster Wecker
- `..._sleep_confidence` — Schlaf-Indikator (0–100)

Abfrage: `ha_search` mit `domain_filter: sensor`, `query: s921`, kompakt via `result_fields`.

## Weitere Tracker im HA

- Legacy: `device_tracker.handy` (SM-G973F, altes S10, meist `unknown`) — verwaist, Aufräumen-Kandidat

## Anker-Standorte / persönliche Details

Adressen und persönliche Ortsbezüge (Wohnheim, Eltern, Großeltern) stehen in
`Zuhause/Obsidian/Persönlich/Standorte.md` (Obsidian Wikilink: [[Standorte]])
→ **dort nachschlagen, nicht hier**. Der Skill hält nur die HA-Technik, damit er public-safe bleibt.

## Regeln

1. Erst `person.hanno` lesen; zur Auflösung `geocoded_location` UND Device-Tracker nutzen.
2. `not_home` heißt nicht „unerreichbar" — Standort über `geocoded_location` bestätigen, NICHT raten.
3. Bei Standortfragen IMMER live abfragen (Sensor-Zustände ändern sich stündlich), niemals aus Skripten/Gedächtnis zitieren.