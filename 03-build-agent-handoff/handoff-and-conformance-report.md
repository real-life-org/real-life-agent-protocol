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
| `issues[]` | offene Issues oder Spec-Fragen |
| `blockers[]` | aktuelle Blocker |
| `residualRisk[]` | bekannte Restrisiken |
| `humanGates[]` | menschliche Entscheidungen |
| `followUps[]` | empfohlene nächste Tasks |

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
| `prStatusLinks` | PR, Checks, Reviews und Summary sind verlinkt |

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
  "humanGateStatus": {
    "status": "open",
    "gates": [
      "Human decides whether GitHub thread classification is trustworthy enough for label automation."
    ]
  },
  "ambiguityPolicyApplied": true,
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
- Wie werden Review-Bot-Zustände normalisiert?
- Wie streng soll `stateTransitionsValid` in Attach-/Refresh-Runs sein?
