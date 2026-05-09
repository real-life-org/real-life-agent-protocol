# Runner Integration

**Status:** Entwurf v0.1
**Datum:** 2026-05-09
**Scope:** Verhältnis zwischen Real Life Agent Protocol und [real-life-org/wot-agent-runner](https://github.com/real-life-org/wot-agent-runner).

---

## 1. Zweck

Diese Spec beschreibt, wie der WoT Agent Runner zum Real Life Agent Protocol steht.

Kurzfassung:

```text
real-life-agent-protocol = Vertrag, Begriffe, Conformance
wot-agent-runner         = erste Referenzimplementierung für rlap-agent-workflow@0.1
```

Der Runner soll das Agent Protocol implementieren oder darauf mappen. Das Agent Protocol darf aber nicht vom Runner abhängen.

## 2. Abgrenzung

Das Agent Protocol definiert:

- Rollen,
- Task-Lifecycle,
- Human Gates,
- Handoff- und Reporting-Erwartungen,
- Ambiguity Policy,
- Conformance-Profile,
- Verhalten von Network Agents.

Der Runner implementiert konkret:

- Task-Dateien,
- Queue und Run-State,
- Worktree-Erzeugung,
- Prompt-Erzeugung,
- Codex-/Claude-Ausführung,
- Checks,
- Audit-Artefakte,
- PR-Erstellung,
- Labels,
- Review-Bot-Integration,
- Agent Runner Summary.

## 3. Implementierte Profile

Der aktuelle Runner ist nach den P0-Slices vom 2026-05-09 Kandidat für:

```text
implements:
- rlap-agent-workflow@0.1
- rlap-task@0.1
- rlap-run-state@0.1
- rlap-handoff@0.1
- rlap-conformance-report@0.1
```

Das ist ein Implementierungs-Claim des [real-life-org/wot-agent-runner](https://github.com/real-life-org/wot-agent-runner), nicht automatisch eine Norm für alle Runner.

Er implementiert NICHT automatisch:

- `rlap-network-agent@0.1`,
- `rlap-memory-consent@0.1`,
- soziale Agentenrollen wie Quest Agent oder Connector Agent.

Der Runner ist ein Build-Agent-Runtime-System, kein sozialer Network Agent.

## 4. Mapping

| RLAP-Konzept | Runner-Entsprechung |
|---|---|
| Task | Task JSON in `tasks/` oder importierter Plan |
| Program Plan | Plan-Dateien und Program-Dokumente |
| Run | `runs/<run-id>/` |
| Run Worktree | `runs/<run-id>/worktree` |
| Prompt Artifact | generierte Implementer-/Reviewer-Prompts |
| Checks | Task-spezifische Check Commands |
| Scope-Gate | Prüfung gegen `allowedScope` / `forbiddenScope` |
| Handoff | Agent Runner Summary |
| State | Runner State JSON und Queue Status |
| Human Gate | `human-gate`, `ready-for-human`, PR bleibt ungemerged |
| GitHub Mirror | PR, Labels, Kommentare, Review-Threads |

## 4.1 P0-Evidence aus dem aktuellen Runner

Der aktuelle Runner-Main enthält nach den gemergten PRs #10-#14 folgende RLAP-relevante Fähigkeiten:

| PR | Fähigkeit | RLAP-Bezug |
|---|---|---|
| [#10](https://github.com/real-life-org/wot-agent-runner/pull/10) | aktuelle unresolved GitHub Review Threads blockieren korrekt | Review Rules, Run State |
| [#11](https://github.com/real-life-org/wot-agent-runner/pull/11) | Program Merge Context aus Task-Dependency-Metadaten | `rlap-task@0.1`, Handoff |
| [#12](https://github.com/real-life-org/wot-agent-runner/pull/12) | Control-Plane Dashboard liest Program Progress aus Handoffs | Handoff als nachgelagertes Agenten-/Dashboard-Artefakt |
| [#13](https://github.com/real-life-org/wot-agent-runner/pull/13) | Attach/Refresh-Modus ohne neue Review-Requests, Label-Sync, Thread-State-Härtung | Run State, Review Orchestration |
| [#14](https://github.com/real-life-org/wot-agent-runner/pull/14) | Review Finding Coverage mit Evidence-Regeln | Handoff, Definition of Done |

Vor diesen PRs wurden bereits Schema-backed Artefakte eingeführt: `rlap-run-state.json`, `rlap-handoff.json`, `rlap-conformance-report.json` und `schemaValidations[]` gegen die RLAP-Schemas.

## 4.2 Reporting-Only Extensions

Einige Runner-Artefakte sind aktuell Runner-spezifische Erweiterungen und keine allgemeinen RLAP-Profile:

| Erweiterung | Bedeutung |
|---|---|
| `runner-program-merge-context@0.1` | Dependency-, Merge-Lane- und Reihenfolgeprojektion für PR-Programme |
| `reviewFindingCoverage` | strukturierte Evidence-Abdeckung für adressierte Review-Findings |
| `integrationStatus` | Projektion, ob PR-Head oder Merge-Commit in der Zielbranch angekommen ist |

Diese Felder sind bewusst `reporting-only`. Sie dürfen Dashboards, Human Maintainer und nachgelagerte Agenten informieren, aber nicht automatisch mergen, retargeten oder fachliche Entscheidungen treffen.

## 5. Integrationsregeln

Der Runner SOLLTE:

- seine Task-JSONs gegen ein `rlap-task@0.1` Schema mappen,
- seine Statuswerte gegen das RLAP-Zustandsmodell mappen,
- seine Agent Runner Summary als `rlap-handoff@0.1` ausdrücken oder daraus ableitbar machen,
- die Ambiguity Policy in Implementer- und Reviewer-Prompts aufnehmen,
- Conformance-Checks für `rlap-agent-workflow@0.1` ausgeben,
- Dependency-/Batch-Informationen als First-Class-Daten modellieren,
- Program-Fortschritt aus PRs, Checks, Coverage, Docs und Human Gates aggregierbar machen.

Der Runner DARF NICHT:

- Agent Protocol Normen allein durch Implementierungsverhalten setzen,
- Auto-Merge als Standard einführen,
- Runner-spezifische Details als allgemeine Agent-Norm ausgeben,
- soziale Network-Agent-Entscheidungen in Build-Workflow-Code verstecken.

## 6. Migration vom Prototype

Der aktuelle Prototyp bleibt wertvoll und wird nicht ersetzt.

Phase 1 SOLLTE keine frühe Rename-/Rebrand-Aktion erzwingen. `wot-agent-runner` kann zunächst so heißen und intern RLAP-konform werden. Die Generalisierung läuft über Program Plans, Task-Schemas, Handoffs und Conformance Reports, nicht über einen neuen Namen.

Sinnvolle Entwicklungsschritte:

1. `rlap-agent-workflow@0.1` in README und PR-Summary referenzieren. Status: umgesetzt.
2. Task-JSON-Felder gegen RLAP-Begriffe dokumentieren. Status: umgesetzt als `rlap-task@0.1`-Schema-Mapping.
3. Ambiguity Policy in Prompts übernehmen. Status: umgesetzt als Review-/Prompt-Regel.
4. Handoff-Felder standardisieren. Status: umgesetzt als `rlap-handoff.json` mit stabilem Summary-Kern.
5. Dependency-/Batch-Modell ergänzen. Status: umgesetzt als Program Merge Context.
6. Status-/Program-Reporting verbessern. Status: umgesetzt als Dashboard-Projektion aus Handoffs.
7. Conformance-Report pro Run erzeugen. Status: umgesetzt inklusive Schema-Validierungen.
8. Review-Finding-Coverage aus PR-Learnings ergänzen. Status: umgesetzt nach PR #13/#14.

Die priorisierte Umsetzungsroadmap steht in [runner-roadmap.md](runner-roadmap.md).

## 7. Offene Fragen

- Soll der Runner in `real-life-org/wot-agent-runner` bleiben oder später allgemeiner benannt werden?
- Wie stark sollen RLAP-Schemas rückwärtskompatibel zu bestehenden Task-JSONs sein?
- Welche Runner-Features sind RLAP-Conformance, welche bleiben Implementierungsdetails?
- Wie werden mehrere Runner-Implementierungen vergleichbar?
- Welche Daten aus Runner-Runs dürfen in andere Agenten-Kontexte übernommen werden?
