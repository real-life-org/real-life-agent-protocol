# wot-agent-runner Build Conformance

**Status:** Conformance-Claim-Kandidat
**Stand:** 2026-05-10
**Runner:** [real-life-org/wot-agent-runner](https://github.com/real-life-org/wot-agent-runner)
**Scope:** Build-Agent-Runtime, nicht Network-Agent-Runtime.

---

## 1. Claim

Der aktuelle `wot-agent-runner` ist die erste Referenzimplementierung für die Build-Agent-Familie des Real Life Agent Protocol.

Er beansprucht als Kandidat:

```text
implements:
- rlap-agent-workflow@0.1
- rlap-task@0.1
- rlap-run-state@0.1
- rlap-handoff@0.1
- rlap-conformance-report@0.1
```

Dieser Claim gilt für die operative Spec-to-Task-to-PR-Arbeit. Er gilt nicht für Network Agents, Memory Consent im RLN, Agent-DIDs oder soziale Rollen wie Quest Agent, Connector Agent oder Crew Agent.

## 2. Evidence

Der Claim stützt sich auf gemergte Runner-Slices:

| Runner PR | Evidence |
|---|---|
| [#10](https://github.com/real-life-org/wot-agent-runner/pull/10) | Current unresolved GitHub Review Threads werden als Blocker behandelt; stale/outdated/resolved Threads werden getrennt. |
| [#11](https://github.com/real-life-org/wot-agent-runner/pull/11) | Dependency- und Merge-Metadaten werden als Program Merge Context in Summary, Handoff und Conformance Report abgebildet. |
| [#12](https://github.com/real-life-org/wot-agent-runner/pull/12) | Control-Plane Dashboard liest Program Progress aus `rlap-handoff.json`-Artefakten. |
| [#13](https://github.com/real-life-org/wot-agent-runner/pull/13) | Attach/Refresh-Modus aktualisiert Checks, Review-State, Summary und Labels ohne neue GitHub Review Requests. |
| [#14](https://github.com/real-life-org/wot-agent-runner/pull/14) | Review Finding Coverage verlangt Evidence oder Begründung für adressierte Findings. |

Zusätzlich erzeugt der Runner pro Run maschinenlesbare Artefakte:

- `rlap-run-state.json`,
- `rlap-handoff.json`,
- `rlap-conformance-report.json`.

Der Conformance Report enthält `schemaValidations[]` für:

- `rlap-task@0.1`,
- `rlap-run-state@0.1`,
- `rlap-handoff@0.1`,
- `rlap-conformance-report@0.1`.

## 3. Grenzen

Der Runner darf trotz dieses Claims nicht:

- automatisch mergen,
- Releases auslösen,
- normative Spec-Entscheidungen treffen,
- soziale Network-Agent-Entscheidungen treffen,
- Menschen bewerten, ranken oder steuern,
- Runner-spezifische Erweiterungen als allgemeine RLAP-Norm ausgeben.

## 4. Runner-Spezifische Erweiterungen

Folgende Felder und Projektionen sind aktuell Runner-spezifisch:

| Erweiterung | Status |
|---|---|
| `runner-program-merge-context@0.1` | reporting-only |
| `reviewFindingCoverage` | Build-Agent-Handoff-Erweiterung, Kandidat für späteres eigenes Schema |
| `integrationStatus` | PR-/Branch-Projektion, reporting-only |
| Control-Plane Program Dashboard | Runner-/Operator-UI, nicht RLAP-Pflicht |

## 5. Offene Conformance-Arbeit

Noch offen:

- maschinenlesbares Conformance-Manifest für Runner-Implementierungen,
- formalisiertes Review-Findings-Schema,
- Beispiele für RLN-, WoT- und RLS-Tasks,
- klare Vergleichsregeln für alternative Runner-Implementierungen,
- Entscheidung, welche Runner-Erweiterungen in spätere RLAP-Profile aufgenommen werden.
