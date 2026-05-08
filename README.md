# Real Life Agent Protocol

Spezifikation für spec-getriebene KI-Agentenarbeit im Real-Life-Ökosystem.

**Status:** Initialer Arbeitsstand
**Start:** 2026-05-09
**Ziel:** Beschreiben, wie KI-Agenten aus Specs konkrete Software, Tests, Playbooks, Quests, Rollout-Artefakte und Spec-Verbesserungen ableiten können, ohne menschliche Entscheidung, soziale Leitplanken oder Conformance zu ersetzen.

---

## Zweck

Dieses Repository beschreibt den Arbeitsrahmen für KI-Agenten, die am Aufbau des Real Life Networks und der zugehörigen Software mitwirken.

Es verallgemeinert das Muster, das bereits mit [real-life-org/wot-spec](https://github.com/real-life-org/wot-spec) und [real-life-org/wot-agent-runner](https://github.com/real-life-org/wot-agent-runner) entsteht:

```text
Vision -> Spec -> Task -> Agentenarbeit -> PR/Artefakt -> Review -> Human Gate -> Feedback in Spec
```

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
| [01-agent-workflow/agent-workflow-spec.md](01-agent-workflow/agent-workflow-spec.md) | Grundspec für agentische Arbeitsprozesse |
| [schemas](schemas/) | Maschinenlesbare Task-, Run- und Handoff-Schemata |
| [examples](examples/) | Beispiel-Tasks, Handoffs und Review-Artefakte |
| [conformance](conformance/) | Prüfbarkeit und Agent-Workflow-Conformance |

## Aktueller Arbeitsfokus

1. Agent Workflow Spec v0.1 stabilisieren.
2. Ein minimales Task-Schema aus der Spec ableiten.
3. Erste Beispiel-Tasks für RLN und WoT anlegen.
4. Conformance-Checklist für Agentenarbeit formulieren.

## Arbeitsweise

- Specs bleiben Quelle der Wahrheit.
- Agenten arbeiten in kleinen, reviewbaren Slices.
- Tasks haben Spec-Referenzen, Scope-Grenzen, Akzeptanzkriterien und Checks.
- Human Gates bleiben verpflichtend für Merge, Release und normative Entscheidungen.
- Offene Fragen werden zurück in Specs, Issues oder Entscheidungslogs gespielt.

## Lizenz

Noch zu entscheiden.
