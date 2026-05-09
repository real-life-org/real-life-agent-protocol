# Build Agent Handoff and Conformance Report

**Status:** Entwurf v0.1
**Datum:** 2026-05-09
**Scope:** Handoff- und Conformance-Report-Profile für Build-Agent-Runs.

---

## 1. Zweck

Ein Build-Agent-Run endet nicht mit "Agent fertig". Er endet mit einem prüfbaren Handoff und, wo möglich, einem maschinenlesbaren Conformance Report.

Dieses Dokument beschreibt:

- `rlap-handoff@0.1`,
- `rlap-conformance-report@0.1`.

Beide Profile gehören zur Build-Agent-Familie.

## 2. Handoff

Ein Handoff ist die menschenlesbare Übergabe eines Runs.

Ein `rlap-handoff@0.1` SOLLTE enthalten:

| Feld | Bedeutung |
|---|---|
| `taskId` | Task-ID |
| `runId` | Run-ID |
| `prUrl` | PR-URL, falls vorhanden |
| `commits[]` | relevante Commit-Hashes |
| `specRefs[]` | betroffene Specs |
| `changedFiles[]` | geänderte Dateien |
| `acceptanceStatus[]` | Status der Akzeptanzkriterien |
| `checks[]` | ausgeführte Checks und Ergebnis |
| `reviewStatus` | lokale und GitHub-Review-Sicht |
| `reviewStatus.findingCoverage` | Status offener, behobener, nicht anwendbarer und human-gate-pflichtiger Findings |
| `reviewStatus.threadResolution` | Status aktueller, stale/outdated und resolved Review Threads |
| `integrationStatus` | Projektion des PR-/Branch-Zustands gegen die Zielbranch |
| `programMergeContext` | Dependency-, Merge-Group- und Reihenfolgekontext |
| `artifacts` | Pfade zu State, Prompts, Logs, Audit-Artefakten und maschinenlesbaren RLAP-Artefakten |
| `summary` | kompakter maschinenlesbarer Überblick für Dashboards oder nachgelagerte Agenten |
| `issues[]` | offene Issues oder Spec-Fragen |
| `blockers[]` | aktuelle Blocker |
| `residualRisk[]` | bekannte Restrisiken |
| `humanGates[]` | menschliche Entscheidungen |
| `followUps[]` | empfohlene nächste Tasks |

Der aktuelle [wot-agent-runner](https://github.com/real-life-org/wot-agent-runner) schreibt `reviewStatus.findingCoverage` als kompakten Status-String. Die zugehörigen Counts liegen im Handoff unter `summary.reviewer`.

Aktuelle Runner-kompatible Felder:

| Feld | Bedeutung |
|---|---|
| `reviewStatus.findingCoverage` | `not-run`, `no-findings`, `open`, `covered`, `incomplete`, `needs-human` oder `invalid` |
| `summary.reviewer.findingCoverageStatus` | gleicher Coverage-Status für kompakte Dashboard-Auswertung |
| `summary.reviewer.findingResolutionCount` | Anzahl strukturierter Resolutionen |
| `summary.reviewer.openFindingCount` | Anzahl aktueller offener Findings |

Eine Umsetzung SOLLTE `covered` nur melden, wenn keine aktuellen offenen Findings mehr existieren und alle Resolutionen die geforderte Evidence oder Begründung tragen. Ein späteres eigenes Review-Findings-Schema KANN daraus ein reichhaltigeres Objekt machen; bis dahin müssen Handoff-Consumer die aktuelle Runner-Form unterstützen.

## 3. Maschinenlesbarer Conformance Report

Ein Conformance Report ist keine Prosa. Er ist ein maschinenlesbares Artefakt, das pro Run erzeugt werden kann.

Ein `rlap-conformance-report@0.1` SOLLTE mindestens prüfen:

| Feld | Bedeutung |
|---|---|
| `taskSchemaValid` | Task erfüllt `rlap-task@0.1` |
| `stateTransitionsValid` | Run-Zustände sind gültig |
| `checksExecuted` | boolesches Signal, ob konfigurierte Checks ausgeführt wurden |
| `checks` | ausgeführte oder geplante Checks mit Ergebnis |
| `scopeGateResult` | Scope-Gate-Ergebnis |
| `handoffSchemaValid` | Handoff erfüllt `rlap-handoff@0.1` |
| `humanGateStatus` | Human Gates sind offen, erfüllt oder nicht anwendbar |
| `ambiguityPolicyApplied` | boolesches Signal, ob Spec/Domain/Implementation-Klärung angewandt wurde |
| `schemaValidations[]` | Schema-ID, Version, Artefaktpfad, Ergebnis und Fehler pro geprüftem RLAP-Artefakt |
| `prStatusLinks` | PR, Checks, Reviews und Summary sind verlinkt |

`schemaValidations[]` ist nicht optionaler Fließtext. Ein Runner, der Schema-backed Conformance behauptet, MUSS pro geprüftem Artefakt ausweisen:

- Profil,
- Schema-ID,
- Schema-Version,
- Schema-Pfad,
- Artefaktpfad,
- Validitätsstatus,
- Fehlerliste.

Mindestens erwartet werden Validierungen für:

- `rlap-task@0.1`,
- `rlap-run-state@0.1`,
- `rlap-handoff@0.1`,
- `rlap-conformance-report@0.1`.

## 4. Beispiel

```json
{
  "profile": "rlap-conformance-report@0.1",
  "generatedAt": "2026-05-09T10:30:00Z",
  "runner": {
    "name": "wot-agent-runner-prototype",
    "repository": "https://github.com/real-life-org/wot-agent-runner",
    "implements": ["rlap-agent-workflow@0.1"]
  },
  "run": {
    "taskId": "runner-review-thread-current-state",
    "runId": "runner-review-thread-current-state-2026-05-09T10-30-00Z",
    "decisionStatus": "ready-for-human",
    "rlapRunState": "ready-for-human"
  },
  "taskSchemaValid": true,
  "stateTransitionsValid": true,
  "checksExecuted": true,
  "checks": {
    "executed": true,
    "status": "pass",
    "results": [
      { "command": "npm test", "status": "pass" },
      { "command": "git diff --check", "status": "pass" }
    ]
  },
  "scopeGateResult": {
    "status": "pass",
    "violationCount": 0,
    "violations": []
  },
  "handoffSchemaValid": true,
  "programMergeContext": {
    "profile": "runner-program-merge-context@0.1",
    "mergeGroup": "runner-rlap-p0",
    "recommendedMergeOrder": 8,
    "mergeReadiness": "requires-dependency-verification"
  },
  "humanGateStatus": {
    "status": "open",
    "gates": [
      "Human decides whether GitHub thread classification is trustworthy enough for label automation."
    ]
  },
  "ambiguityPolicyApplied": true,
  "schemaValidations": [
    {
      "profile": "rlap-task@0.1",
      "schemaId": "https://raw.githubusercontent.com/real-life-org/real-life-agent-protocol/main/schemas/rlap-task-0.1.schema.json",
      "schemaVersion": "0.1",
      "schemaPath": "../real-life-agent-protocol/schemas/rlap-task-0.1.schema.json",
      "artifactPath": "tasks/runner-review-thread-current-state.json",
      "valid": true,
      "errors": []
    }
  ],
  "prStatusLinks": {
    "prUrl": "https://github.com/real-life-org/wot-agent-runner/pull/123",
    "summaryPath": "runs/.../github-run-summary.md",
    "statePath": "runs/.../state.json",
    "conformanceReportPath": "runs/.../rlap-conformance-report.json"
  },
  "summary": {
    "valid": true,
    "status": "ready-for-human"
  }
}
```

## 5. Statuswerte

Ein Conformance Report SOLLTE einen dieser Gesamtstatus ausgeben:

| Status | Bedeutung |
|---|---|
| `passed` | konform und kein Human Gate offen |
| `ready-for-human` | konform, aber menschliche Entscheidung nötig |
| `fix-required` | Checks, Scope, Review oder Schema verlangen Änderung |
| `human-gate` | Runner kann ohne menschliche Entscheidung nicht fortfahren |
| `not-conformant` | RLAP-Anforderungen wurden verletzt |

## 6. Offene Fragen

- Welche Felder gehören in das portable Handoff-Schema und welche bleiben Runner Summary?
- Soll der Conformance Report im PR kommentiert oder nur als Audit-Artefakt gespeichert werden?
- Wie streng soll `stateTransitionsValid` in Attach-/Refresh-Runs sein?
- Welche Review-Finding-Coverage-Felder sollen langfristig in ein eigenes Schema ausgelagert werden?
- Welche Program-Merge-Context-Felder sind allgemeines RLAP und welche bleiben Runner-spezifische Erweiterung?
