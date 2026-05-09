# Conformance

Conformance beschreibt, wann ein Runner, Agent, Task-Format oder Workflow behaupten darf, das Real Life Agent Protocol zu unterstützen.

RLAP unterscheidet zwei Profilfamilien:

| Familie | Profile | Zweck |
|---|---|---|
| Build-Agent-Profile | `rlap-agent-workflow`, `rlap-task`, `rlap-run-state`, `rlap-handoff`, `rlap-conformance-report` | Specs, Code, Tests, PRs, Reviews und Runner-Arbeit |
| Network-Agent-Profile | `rlap-network-agent`, `rlap-memory-consent`, Rollen, Agent Identity, Attestations | Agenten im lebendigen Real Life Network |

Diese Familien sind verwandt, aber nicht dieselbe Runtime.

## `rlap-agent-workflow@0.1`

Eine Umsetzung ist `rlap-agent-workflow@0.1`-konform, wenn sie:

- Task-Scope, Spec-Referenzen und Akzeptanzkriterien explizit macht,
- Agentenarbeit reviewbar und auditierbar macht,
- Definition of Ready und Definition of Done abbildet,
- Scope-Gates oder gleichwertige Prüfungen nutzt,
- menschliche Gates für Merge, Release und normative Entscheidungen respektiert,
- Spec Ambiguity, Domain Mapping und Implementation Design unterscheidet,
- offene Fragen zurück in Specs, Issues oder Entscheidungslogs spielt,
- Conformance und soziale Leitplanken prüft,
- keine Menschen bewertet oder steuert,
- keine sensiblen Daten ohne Grundlage in Prompts, Logs oder PRs trägt.

## `rlap-task@0.1`

Ein Task-Format ist `rlap-task@0.1`-konform, wenn es:

- Task-ID, Titel, Kind, Repo, Spec-Refs, Scope, Acceptance, Checks und Human Gates enthält,
- `allowedScope` und `forbiddenScope` abbildet,
- Dependency-/Merge-Metadaten wie `dependsOn`, `blocks`, `mergeGroup`, `recommendedMergeOrder`, `rebaseRequired` und `supersedes` tragen kann,
- keine Network-Agent-Entscheidungen in Build-Tasks versteckt.

## `rlap-handoff@0.1`

Ein Handoff ist `rlap-handoff@0.1`-konform, wenn es:

- Task-ID, Run-ID, PR-URL, Commits und Spec-Refs ausweist,
- Checks, Scope-Gate, Review-Status und Human Gates getrennt darstellt,
- Blocker, Issues, Residual Risk und Follow-ups klar benennt,
- zwischen Ergebnis, Annahme und offener Entscheidung unterscheidet.

## `rlap-run-state@0.1`

Ein Run-State-Mapping ist `rlap-run-state@0.1`-konform, wenn es:

- die portablen Zustände `proposed`, `queued`, `running`, `checks-failed`, `fix-required`, `ready-for-human`, `human-gate`, `merged`, `superseded`, `closed` und `completed` abbilden kann,
- Statusquellen wie Checks, Scope-Gate, Review, Threads, Human Gates und PR-Zustand getrennt betrachtet,
- stale/outdated Kommentare nicht als aktuelle Blocker zählt,
- GitHub Labels nur als Projektion und nicht als alleinige Quelle der Wahrheit behandelt.

## `rlap-conformance-report@0.1`

Ein Conformance Report ist `rlap-conformance-report@0.1`-konform, wenn er maschinenlesbar ausweist:

- `taskSchemaValid`,
- `stateTransitionsValid`,
- `checksExecuted`,
- `scopeGate`,
- `handoffSchemaValid`,
- `humanGateStatus`,
- `ambiguityPolicyApplied`,
- `prStatusLinks`.

## `rlap-network-agent@0.1`

Eine Umsetzung ist `rlap-network-agent@0.1`-konform, wenn sie:

- Agenten als Agenten erkennbar macht,
- nur aus sichtbarem und zweckmäßigem Kontext handelt,
- Vorschläge als freiwillige Einladungen formuliert,
- Ablehnung und Nicht-Reaktion respektiert,
- Sichtbarkeit und Zustimmung achtet,
- Menschen nicht rankt oder steuert,
- Human Gates für Vorstellungen, sensible Informationen, Konflikt, Kinder/Jugendliche und Sichtbarkeitserweiterung nutzt.

## `rlap-memory-consent@0.1`

Eine Umsetzung ist `rlap-memory-consent@0.1`-konform, wenn sie:

- Session Context, Personal Memory, Space Memory, Artifact Memory und Public Knowledge unterscheidet,
- Zustimmung zweckgebunden behandelt,
- Weitergabe und Veröffentlichung trennt,
- sensible Informationen minimiert,
- Korrektur, Einschränkung oder Vergessen unterstützt,
- keine verdeckten Profile oder Scores erzeugt.

## `rlap-runner-integration@0.1`

Eine Runner-Implementierung ist `rlap-runner-integration@0.1`-konform, wenn sie:

- ihre Task-, Run-State- und Handoff-Artefakte auf RLAP-Begriffe mappen kann,
- `rlap-agent-workflow@0.1` als implementiertes Profil ausweisen kann,
- Scope-Gates, Checks, Review und Human Gates auditierbar macht,
- keine Runner-spezifischen Details als allgemeine Agent-Norm ausgibt,
- Auto-Merge nicht als Standardverhalten nutzt.

## Noch offen

- Maschinenlesbares Conformance-Manifest.
- Task-Schema-Validatoren.
- Dependency-/Batch-Schema.
- Handoff-Schema.
- Review-Findings-Schema.
- Role-Schema.
- Beispiele für RLN-, WoT- und RLS-Tasks.
