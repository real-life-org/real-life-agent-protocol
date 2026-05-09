# Network Agent Roles

**Status:** Entwurf v0.1
**Datum:** 2026-05-09
**Scope:** Rollenprofile für Agenten im lebendigen Real Life Network.

---

## 1. Zweck

Network Agents sollen nicht als unklare Allzweck-Akteure auftreten. Eine Rolle beschreibt, was ein Agent im Netzwerk tun darf, welchen sichtbaren Kontext er braucht, welche Human Gates gelten und woran Conformance geprüft wird.

Build-Agent-Rollen wie Program Operator, Implementation Agent oder Review Agent gehören zur Build-Agent-Familie und sind in [01-build-agent-workflow](../01-build-agent-workflow/agent-workflow-spec.md) beschrieben.

## 2. Rollenprinzipien

Eine Agentenrolle MUSS:

- erkennbar sein,
- Zweck und Grenzen benennen,
- benötigten Kontext minimieren,
- erlaubte Handlungen definieren,
- Human Gates enthalten,
- mit Conformance-Profilen verknüpft sein.

Ein Agent KANN mehrere Rollen haben. In einem konkreten Run oder einer konkreten Interaktion SOLLTE aber klar sein, in welcher Rolle er handelt.

## 3. Network-Agent-Rollen

Diese Rollen handeln im lebendigen Real Life Network. Sie implementieren vor allem [network-agent-behavior](../10-network-agent-behavior/network-agent-behavior.md) und [memory-consent](../11-memory-consent/memory-consent.md).

| Rolle | Zweck | Darf nicht |
|---|---|---|
| Crew Agent | Crew im Pax-/Event-Kontext unterstützen | Crew-Entscheidungen ersetzen |
| Quest Agent | passende Handlungseinladungen vorschlagen | Quests als Pflicht oder Score formulieren |
| Connector Agent | Menschen, Initiativen, Ressourcen und Bedürfnisse behutsam verbinden | Menschen ohne Zustimmung direkt vorstellen |
| Reflection Agent | Menschen bei Orientierung und Selbstklärung begleiten | therapeutische oder autoritative Rolle behaupten |
| Host Agent | Hosts bei Vorbereitung, Einladung und Nachbereitung unterstützen | lokale Verantwortung übernehmen |
| Documentation Agent | Geschehen zusammenfassen und sichtbar machen | Zustimmung bei sensibler Dokumentation umgehen |
| Commons Agent | Commons, Ressourcen und Pflegehinweise sichtbar machen | Eigentum, Pflege oder Nutzungsregeln zentral festlegen |

## 4. Rollenprofil

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

## 5. Offene Fragen

- Welche Rollen brauchen maschinenlesbare Prompt-Contracts?
- Welche Rollen bekommen eigene DIDs oder Profile?
- Wie werden Rollen attestiert?
- Welche Rollen sind für Pax v0.1 wirklich nötig?
- Wie werden Agentenrollen in Apps sichtbar gemacht?
