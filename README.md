# Real Life Agent Protocol

Spezifikation für KI-Agenten im Real-Life-Ökosystem: agentische Arbeit an Specs, Code und Artefakten sowie späteres Handeln im lebendigen Real Life Network.

**Status:** Initialer Arbeitsstand
**Start:** 2026-05-09
**Ziel:** Beschreiben, wie KI-Agenten kontrolliert arbeiten, sich in Netzwerk-Kontexte einbetten, menschliche Entscheidung respektieren und durch Conformance prüfbar bleiben.

---

## Zweck

Dieses Repository beschreibt den gemeinsamen Rahmen für KI-Agenten, die am Aufbau des Real Life Networks und der zugehörigen Software mitwirken.

Es verallgemeinert das Muster, das bereits mit [real-life-org/wot-spec](https://github.com/real-life-org/wot-spec) und [real-life-org/wot-agent-runner](https://github.com/real-life-org/wot-agent-runner) entsteht:

```text
Vision -> Spec -> Task -> Agentenarbeit -> PR/Artefakt -> Review -> Human Gate -> Feedback in Spec
```

Die erste Spec ist bewusst pragmatisch: ein Runner Workflow Contract für kleine Tasks, harte Scope-Grenzen, Review, Human Gates, Merge-Disziplin und die Trennung von Protokollfragen, Domain-Mapping und Implementierungsdesign.

Der Runner wird dabei als operative Steuerzentrale verstanden, nicht nur als Script-Starter.

Der größere Rahmen unterscheidet zwei Agentenfamilien:

| Familie | Zweck |
|---|---|
| Build Agents | Specs, Code, Tests, PRs, Playbooks und Artefakte erzeugen oder prüfen. |
| Network Agents | Menschen, Crews, Kreise, Quests, Orte, Ressourcen und Follow-ups im Netzwerk unterstützen. |

## Abgrenzung

| Repository | Rolle |
|---|---|
| [real-life-org/wot-spec](https://github.com/real-life-org/wot-spec) | Technische Web-of-Trust-Spezifikation |
| [real-life-org/wot-agent-runner](https://github.com/real-life-org/wot-agent-runner) | Runner-/Control-Plane-Prototyp für agentische Spec-zu-PR-Arbeit |
| [real-life-org/real-life-network-protocol](https://github.com/real-life-org/real-life-network-protocol) | Soziales Netzwerkprotokoll, Quests, Praktiken und Rollout |
| [real-life-org/real-life-stack](https://github.com/real-life-org/real-life-stack) | App-/UI-Baukasten und Real-Life-Stack-Datenmodell |
| [real-life-org/real-life-agent-protocol](https://github.com/real-life-org/real-life-agent-protocol) | Agenten-Arbeitsprotokoll: Tasks, Runs, Reviews, Human Gates und Feedback in Specs |

## Spezifikations-Landkarte

| Bereich | Zweck |
|---|---|
| [01-agent-workflow/agent-workflow-spec.md](01-agent-workflow/agent-workflow-spec.md) | Spec-to-Task-to-PR Workflow aus der WoT-Runner-Praxis |
| [02-network-agent-behavior/network-agent-behavior.md](02-network-agent-behavior/network-agent-behavior.md) | Verhalten von Agenten im lebendigen RLN-Kontext |
| [03-agent-memory-consent/memory-consent.md](03-agent-memory-consent/memory-consent.md) | Erinnerung, Sichtbarkeit und Zustimmung |
| [04-agent-roles/roles.md](04-agent-roles/roles.md) | Build- und Network-Agent-Rollen |
| [05-runner-integration/runner-integration.md](05-runner-integration/runner-integration.md) | Verhältnis zwischen RLAP und WoT Agent Runner |
| [schemas](schemas/) | Maschinenlesbare Task-, Run- und Handoff-Schemata |
| [examples](examples/) | Beispiel-Tasks, Handoffs und Review-Artefakte |
| [conformance](conformance/) | Prüfbarkeit von Workflow-, Network-, Memory- und Runner-Conformance |

## Aktueller Arbeitsfokus

1. `01-agent-workflow` als Runner-kompatiblen Workflow Contract stabilisieren.
2. `05-runner-integration` auf den bestehenden WoT Agent Runner anwenden.
3. `02-network-agent-behavior` für Pax, lokale Kreise und Quest-Vorschläge konkretisieren.
4. Ein minimales Task-, Handoff- und Role-Schema ableiten.
5. Erste Beispiel-Tasks für RLN und WoT anlegen.

## Arbeitsweise

- Specs bleiben Quelle der Wahrheit.
- Agenten arbeiten in kleinen, reviewbaren Slices.
- Tasks haben Spec-Referenzen, Scope-Grenzen, Akzeptanzkriterien und Checks.
- Human Gates bleiben verpflichtend für Merge, Release und normative Entscheidungen.
- Offene Fragen werden zurück in Specs, Issues oder Entscheidungslogs gespielt.
- Agenten unterscheiden Spec Ambiguity, Domain Mapping und Implementation Design.
- Agenten handeln nur aus sichtbarem Kontext und mit angemessener Zustimmung.

## Lizenz

Noch zu entscheiden.
