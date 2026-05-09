# Build Agent Run State

**Status:** Entwurf v0.1
**Datum:** 2026-05-09
**Scope:** Zustandsmodell für Build-Agent-Runs.

---

## 1. Zweck

`rlap-run-state@0.1` beschreibt den portablen Zustand eines Build-Agent-Runs.

Runner können eigene interne States haben. Für RLAP-Konformität SOLLTEN sie diese aber auf ein gemeinsames Zustandsmodell mappen können.

## 2. Profil

```text
profile: rlap-run-state@0.1
family: build-agent
```

## 3. Zustände

| Status | Bedeutung |
|---|---|
| `proposed` | Task-Idee existiert, ist aber noch nicht Definition-of-Ready. |
| `queued` | Task ist vorbereitet und kann gestartet werden. |
| `running` | Agent oder Runner arbeitet. |
| `checks-failed` | konfigurierte Checks sind fehlgeschlagen. |
| `fix-required` | Checks, Review oder Scope-Gate verlangen Änderungen. |
| `ready-for-human` | Agentische Arbeit ist abgeschlossen und bereit für menschlichen Review. |
| `human-gate` | menschliche Entscheidung erforderlich. |
| `merged` | PR wurde menschlich gemerged. |
| `superseded` | Task wurde durch neuen Task oder Batch ersetzt. |
| `closed` | Task oder PR wurde bewusst geschlossen. |
| `completed` | Prozess ist abgeschlossen, z.B. nach Merge oder bewusst akzeptiertem Artefakt. |

## 4. Statusquellen

Ein Runner SOLLTE den Status aus getrennten Quellen ableiten:

- Task-Schema-Validierung,
- Scope-Gate,
- lokale Checks,
- lokaler Review,
- GitHub Reviews,
- aktuelle unresolved Threads,
- stale/outdated Kommentare,
- Human Gates,
- PR-/Branch-Zustand.

Ein einzelnes GitHub-Label ist nicht die Quelle der Wahrheit. Es ist eine Projektion des Run-State.

## 5. Transitionen

Eine Umsetzung SOLLTE mindestens diese Übergänge erlauben:

```text
proposed -> queued -> running
running -> checks-failed
running -> fix-required
running -> ready-for-human
running -> human-gate
checks-failed -> running
fix-required -> running
ready-for-human -> merged
ready-for-human -> human-gate
ready-for-human -> fix-required
human-gate -> queued
human-gate -> closed
merged -> completed
queued -> superseded
fix-required -> superseded
```

Späte aktuelle Review-Kommentare DÜRFEN `ready-for-human` wieder zu `fix-required` machen. Stale/outdated Kommentare DÜRFEN das nicht.

## 6. Offene Fragen

- Welche Attach-/Refresh-Transitions brauchen eigene Status?
- Soll `checks-failed` eine Unterform von `fix-required` sein oder eigener Status bleiben?
- Wie werden mehrere PRs in einem `mergeGroup` aggregiert?
- Wie werden `rebaseRequired` und `superseded` in GitHub Labels gespiegelt?
