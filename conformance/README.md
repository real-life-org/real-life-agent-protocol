# Conformance

Conformance beschreibt, wann ein Runner, Agent, Task-Format oder Workflow behaupten darf, das Real Life Agent Protocol zu unterstützen.

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
