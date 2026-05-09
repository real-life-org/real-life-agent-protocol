# Build Agent Task

**Status:** Entwurf v0.1
**Datum:** 2026-05-09
**Scope:** Minimales Task-Profil für Build-Agent-Runtimes.

---

## 1. Zweck

`rlap-task@0.1` beschreibt die kleinste maschinenlesbare Arbeitseinheit für Build Agents.

Ein Task ist kein Projektplan und kein freier Prompt. Er ist ein begrenzter, reviewbarer Slice mit Spec-Referenzen, Scope-Grenzen, Checks, Human Gates und optionalen Dependency-/Merge-Metadaten.

## 2. Profil

```text
profile: rlap-task@0.1
family: build-agent
```

Dieses Profil gehört zur Build-Agent-Familie. Es beschreibt nicht das Verhalten von Network Agents im Real Life Network.

## 3. Mindestfelder

| Feld | Pflicht | Bedeutung |
|---|---|---|
| `id` | ja | stabile Task-ID |
| `title` | ja | kurze menschenlesbare Aufgabe |
| `kind` | empfohlen | `spec`, `implementation`, `test`, `playbook`, `rollout`, `review`, `ops`; ohne Angabe gilt für aktuelle Runner-Kompatibilität `implementation` |
| `repo` | ja* | Zielrepository oder lokaler Repo-Key |
| `repoRoot` | kompatibel | aktueller Alias des `wot-agent-runner`; portable Runtimes SOLLTEN langfristig `repo` verwenden |
| `baseBranch` | empfohlen | Branch, auf dem der Task starten soll |
| `targetBranch` | empfohlen | Branch, der für PR/Run verwendet wird |
| `specRefs[]` | ja | konkrete Spec-Abschnitte oder Dateien |
| `contextRefs[]` | optional | zusätzliche Kontextdateien |
| `allowedScope[]` | ja | Dateien/Module, die geändert werden dürfen |
| `forbiddenScope[]` | empfohlen | Bereiche, die nicht verändert werden dürfen |
| `acceptance[]` | ja | prüfbare Akzeptanzkriterien |
| `checks[]` | ja | auszuführende Checks |
| `humanGates[]` | ja | Entscheidungen, die nicht automatisiert werden dürfen |

\* Für `rlap-task@0.1` MUSS entweder `repo` oder der Kompatibilitätsalias `repoRoot` gesetzt sein.

## 4. Dependency- und Merge-Metadaten

Task-Abhängigkeiten sind früh wichtig, weil viele gute Einzel-PRs später durch Basisänderungen dirty werden können.

Ein Task SOLLTE diese Felder unterstützen:

| Feld | Bedeutung |
|---|---|
| `dependsOn[]` | Task-IDs oder PRs, die vorher landen sollten |
| `blocks[]` | Task-IDs oder PRs, die durch diesen Task blockiert werden |
| `mergeGroup` | logische Gruppe, die zusammen geplant oder gebatched wird |
| `recommendedMergeOrder` | Zahl oder String zur empfohlenen Reihenfolge |
| `rebaseRequired` | boolesches Signal, dass Task/PR neu basiert werden muss |
| `supersedes[]` | Tasks/PRs, die durch diesen Task ersetzt werden |

Diese Felder sind kein automatischer Merge-Befehl. Sie dienen Program Operator, Runner und Human Maintainer als Planungsdaten.

## 5. Beispiel

```json
{
  "id": "runner-review-thread-current-state",
  "title": "Classify current vs stale GitHub review threads",
  "kind": "implementation",
  "repo": "wot-agent-runner",
  "baseBranch": "main",
  "targetBranch": "feat/review-thread-state",
  "specRefs": [
    "real-life-agent-protocol/05-build-runner-integration/runner-roadmap.md#31-aktuelle-review-blocker-korrekt-bestimmen"
  ],
  "contextRefs": [
    "docs/github-loop.md",
    "docs/control-plane.md"
  ],
  "allowedScope": [
    "src/",
    "docs/"
  ],
  "forbiddenScope": [
    "../wot-spec/",
    "../real-life-agent-protocol/"
  ],
  "acceptance": [
    "Current non-outdated review threads block.",
    "Stale/outdated bot comments are reported but not blocking.",
    "Runner summary separates current unresolved threads from stale comments."
  ],
  "checks": [
    "npm test",
    "git diff --check"
  ],
  "humanGates": [
    "Human decides whether GitHub thread classification is trustworthy enough for label automation."
  ],
  "dependsOn": [],
  "blocks": [
    "runner-fix-required-label-cleanup"
  ],
  "mergeGroup": "runner-review-status-p0",
  "recommendedMergeOrder": 10,
  "rebaseRequired": false,
  "supersedes": []
}
```

## 6. Definition of Ready

Ein `rlap-task@0.1` ist ready, wenn:

- Pflichtfelder vorhanden sind,
- Scope eng genug ist,
- Checks ausführbar sind oder bewusst als nicht ausführbar markiert werden,
- Human Gates gesetzt sind,
- bekannte Spec Ambiguities verlinkt sind,
- Dependency-/Merge-Metadaten nicht im Widerspruch stehen.

## 7. Offene Fragen

- Soll `repo` ein GitHub-Slug, lokaler Pfad oder stabiler Program-Key sein?
- Wie werden PR-URLs und Task-IDs in `dependsOn` gemeinsam normalisiert?
- Soll `recommendedMergeOrder` numerisch oder deklarativ sein?
- Wann wird der Kompatibilitätsalias `repoRoot` im Runner durch `repo` ersetzt?
- Welche weiteren Task-Felder sind Runner-spezifisch und welche gehören ins portable RLAP-Schema?
