# Agent Roles

**Status:** Entwurf v0.1
**Datum:** 2026-05-09
**Scope:** Rollenprofile für Agenten im Real-Life-Ökosystem.

---

## 1. Zweck

Agenten sollen nicht als unklare Allzweck-Akteure auftreten. Eine Rolle beschreibt, was ein Agent tun darf, welchen Kontext er braucht, welche Human Gates gelten und woran Conformance geprüft wird.

## 2. Rollenprinzipien

Eine Agentenrolle MUSS:

- erkennbar sein,
- Zweck und Grenzen benennen,
- benötigten Kontext minimieren,
- erlaubte Handlungen definieren,
- Human Gates enthalten,
- mit Conformance-Profilen verknüpft sein.

Ein Agent KANN mehrere Rollen haben. In einem konkreten Run oder einer konkreten Interaktion SOLLTE aber klar sein, in welcher Rolle er handelt.

## 3. Build-Agent-Rollen

Diese Rollen arbeiten an Specs, Code, Tests, Playbooks und PRs. Sie implementieren vor allem [rlap-agent-workflow@0.1](../01-agent-workflow/agent-workflow-spec.md).

| Rolle | Zweck | Typische Artefakte |
|---|---|---|
| Program Operator | Programmstatus, Tasks, Merge-Reihenfolge und Human Gates koordinieren | Program Plan, Queue, Statusbericht |
| Spec Agent | Specs strukturieren und klären | Spec PR, offene Frage, Decision Note |
| Implementation Agent | Code oder konkrete Artefakte erzeugen | PR, Patch, Schema |
| Test Agent | Tests, Vektoren und Conformance-Cases erzeugen | Test, Vector, Validation |
| Review Agent | Scope, Checks, Conformance und Risiken prüfen | Review Finding, Handoff |
| Documentation Agent | Handoffs, Changelog und Lernnotizen pflegen | Summary, Changelog, Docs |

## 4. Network-Agent-Rollen

Diese Rollen handeln im lebendigen Real Life Network. Sie implementieren vor allem [network-agent-behavior](../02-network-agent-behavior/network-agent-behavior.md) und [memory-consent](../03-agent-memory-consent/memory-consent.md).

| Rolle | Zweck | Darf nicht |
|---|---|---|
| Crew Agent | Crew im Pax-/Event-Kontext unterstützen | Crew-Entscheidungen ersetzen |
| Quest Agent | passende Handlungseinladungen vorschlagen | Quests als Pflicht oder Score formulieren |
| Connector Agent | Menschen, Initiativen, Ressourcen und Bedürfnisse behutsam verbinden | Menschen ohne Zustimmung direkt vorstellen |
| Reflection Agent | Menschen bei Orientierung und Selbstklärung begleiten | therapeutische oder autoritative Rolle behaupten |
| Host Agent | Hosts bei Vorbereitung, Einladung und Nachbereitung unterstützen | lokale Verantwortung übernehmen |
| Documentation Agent | Geschehen zusammenfassen und sichtbar machen | Zustimmung bei sensibler Dokumentation umgehen |
| Commons Agent | Commons, Ressourcen und Pflegehinweise sichtbar machen | Eigentum, Pflege oder Nutzungsregeln zentral festlegen |

## 5. Rollenprofil

Ein Rollenprofil SOLLTE enthalten:

| Feld | Bedeutung |
|---|---|
| `roleId` | stabile Rollenkennung |
| `purpose` | Zweck der Rolle |
| `visibleContext` | Kontext, den die Rolle braucht |
| `allowedActions` | erlaubte Handlungen |
| `forbiddenActions` | verbotene Handlungen |
| `humanGates` | Situationen, die menschliche Entscheidung erfordern |
| `conformanceProfiles` | relevante RLAP-/RLN-/WoT-Profile |
| `handoff` | was die Rolle dokumentieren muss |

Beispiel:

```json
{
  "roleId": "quest-agent",
  "purpose": "Freiwillige Handlungseinladungen im sichtbaren Kontext vorschlagen.",
  "visibleContext": ["profile-tags", "space", "event", "visible-quests"],
  "allowedActions": ["suggestQuest", "explainQuest", "hideQuest"],
  "forbiddenActions": ["rankPeople", "forceCompletion", "publishWithoutConsent"],
  "humanGates": ["sensitiveQuest", "childrenOrYouth", "visibilityExpansion"],
  "conformanceProfiles": ["rlap-network-agent@0.1", "rlnp-quests@0.1"]
}
```

## 6. Offene Fragen

- Welche Rollen brauchen maschinenlesbare Prompt-Contracts?
- Welche Rollen bekommen eigene DIDs oder Profile?
- Wie werden Rollen attestiert?
- Welche Rollen sind für Pax v0.1 wirklich nötig?
- Wie werden Agentenrollen in Apps sichtbar gemacht?
