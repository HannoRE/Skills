---
name: test-roundtrip
description: Trockenlauf-Test,Roundtrip Hetzner → GitHub → Marvin → Hermes.
---

# Test-Roundtrip

Trockenlauf-Skill. Wird angelegt, um den bidirektionalen Sync zu verifizieren:

1. Hetzner-Checkout in `/home/hermes/repos/Hanno/Skills`
2. Push nach `github.com/HannoRE/Skills`
3. Pull über den Timer auf Marvin
4. Hermes-Laden über `external_dirs` in `/opt/data/external-skills/hanno-skills`

Wenn dieser Skill nach ein paar Minuten in `skill_view(name='test-roundtrip')` auftaucht, ist der Roundtrip in beide Richtungen nicht nur gelesen, sondern auch geschrieben verifiziert.

Nach Bestätigung wird der Skill wieder gelöscht.
