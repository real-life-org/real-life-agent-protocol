# Agent Workflow Spec

**Status:** Entwurf v0.1
**Datum:** 2026-05-09
**Scope:** Arbeitsprotokoll für KI-Agenten, die aus Specs Software, Tests, Playbooks, Quests, Rollout-Artefakte oder Spec-Verbesserungen ableiten.

---

## 1. Zweck

Diese Spec beschreibt, wie KI-Agenten spec-konform am Aufbau des Real Life Networks und der zugehörigen Software arbeiten können.

Sie verallgemeinert das Muster, das bereits mit [real-life-org/wot-spec](https://github.com/real-life-org/wot-spec) und [real-life-org/wot-agent-runner](https://github.com/real-life-org/wot-agent-runner) entsteht:

```text
Vision -> Spec -> Task -> Agentenarbeit -> PR/Artefakt -> Review -> Human Gate -> Feedback in Spec
```

Das Ziel ist nicht, Menschen aus dem Prozess zu entfernen. Das Ziel ist, Agenten so einzubetten, dass sie klar begrenzte, prüfbare Arbeit leisten können, ohne die soziale, technische oder normative Richtung selbst zu setzen.

## 2. Nicht-Ziele

Diese Spec definiert NICHT:

- ein konkretes Runner-CLI,
- ein bestimmtes Agentenmodell,
- Auto-Merge,
- autonome Produktentscheidungen,
- zentrale Steuerung des Netzwerks,
- ein vollständiges Projektmanagement-System,
- eine Ersatzautorität für menschliche Entscheidung.

Der bestehende WoT Agent Runner ist eine Referenzpraxis, aber nicht die einzige erlaubte Umsetzung.

## 3. Grundprinzipien

Ein agentischer Arbeitsprozess MUSS:

1. Specs als Quelle der Wahrheit behandeln.
2. Aufgaben klein genug schneiden, dass sie reviewbar bleiben.
3. Spec-Referenzen, Akzeptanzkriterien und erlaubten Scope explizit machen.
4. menschliche Gates für Merge, Release, normative Entscheidungen und soziale Risiken respektieren.
5. Ergebnisse als PR, Patch, Issue, Playbook, Schema, Test oder dokumentiertes Artefakt sichtbar machen.
6. offene Fragen zurück in Specs, Issues oder Entscheidungslogs spielen.
7. Conformance prüfen, nicht nur Ausführung.
8. Audit-Artefakte erzeugen, wenn Agenten Code, Datenmodelle oder normative Texte verändern.

Ein agentischer Arbeitsprozess DARF NICHT:

- still gegen Specs improvisieren,
- Aufgaben ohne Scope-Grenze ausführen,
- Menschen ranken oder sozialen Druck erzeugen,
- sensible Daten ohne explizite Grundlage in Prompts, Logs oder PRs tragen,
- menschliche Zustimmung durch automatisierte Entscheidung ersetzen,
- Merge, Release oder produktive Datenmigration ohne Human Gate auslösen.

## 4. Begriffe

| Begriff | Bedeutung |
|---|---|
| Spec | Normative oder operative Beschreibung eines Protokolls, Flows, Datenmodells, Playbooks oder sozialen Prozesses. |
| Program | Länger laufender Arbeitsstrom, z.B. "WoT TypeScript Reference Implementation" oder "Pax v0.1 Rollout". |
| Slice | Klar begrenzter Ausschnitt eines Programms, der in einem PR oder Artefakt erledigt werden kann. |
| Task | Maschinenlesbare oder strukturierte Beschreibung eines Slice. |
| Run | Konkrete Ausführung eines Tasks durch Runner, Agenten oder Menschen. |
| Worker | Agent oder Mensch, der einen Task implementiert, dokumentiert oder prüft. |
| Reviewer | unabhängige Instanz, die Ergebnis, Scope, Tests und Conformance prüft. |
| Human Gate | Stelle, an der eine menschliche Entscheidung erforderlich ist. |
| Handoff | Zusammenfassung, mit der ein Agent Ergebnis, Checks, Risiken und offene Fragen übergibt. |

## 5. Workflow

### 5.1 Program definieren

Ein Program beschreibt das größere Ziel, den Kontext und die Phasen.

Beispiele:

- WoT TypeScript Referenzimplementierung.
- Pax/Festival v0.1 Rollout.
- Real Life Network Quest-Templates.
- Real Life Stack RLS-Item-Views.

Ein Program SOLLTE enthalten:

- Zielbild,
- Nicht-Ziele,
- betroffene Repositories,
- relevante Specs,
- Phasen,
- Conformance-Kriterien,
- offene Entscheidungen.

### 5.2 Slice schneiden

Ein Slice ist die kleinste sinnvolle Arbeitseinheit, die ein Agent bearbeiten kann.

Ein guter Slice:

- hat klare Spec-Referenzen,
- hat begrenzten Schreib-Scope,
- hat Akzeptanzkriterien,
- hat passende Checks,
- kann in einem PR oder Artefakt reviewed werden,
- braucht keine große implizite Kontextdeutung.

Schlechte Slices sind:

- "Implementiere das ganze Protokoll",
- "Mach die App fertig",
- "Verbessere die Kultur",
- "Bau alles, was in der Spec steht".

### 5.3 Task formulieren

Ein Task SOLLTE mindestens enthalten:

| Feld | Bedeutung |
|---|---|
| `id` | stabile Task-ID |
| `title` | kurze menschenlesbare Aufgabe |
| `kind` | `spec`, `implementation`, `test`, `playbook`, `rollout`, `review` |
| `repo` | Zielrepository |
| `specRefs[]` | konkrete Spec-Abschnitte oder Dateien |
| `contextRefs[]` | zusätzliche Orientierung, die in den Prompt gehört |
| `allowedScope[]` | Dateien/Module, die geändert werden dürfen |
| `forbiddenScope[]` | Bereiche, die nicht verändert werden dürfen |
| `acceptance[]` | prüfbare Akzeptanzkriterien |
| `checks[]` | Kommandos oder Prüfungen |
| `humanGates[]` | Entscheidungen, die nicht automatisiert werden dürfen |

Beispiel:

```json
{
  "id": "rln-quest-card-templates-pax-p0",
  "title": "Define Pax P0 quest card templates",
  "kind": "spec",
  "repo": "real-life-network-protocol",
  "specRefs": [
    "05-quests/quest-katalog.md",
    "05-quests/quest-mechanik.md",
    "06-data-model/operations-mapping.md"
  ],
  "allowedScope": [
    "05-quests/"
  ],
  "forbiddenScope": [
    "06-data-model/"
  ],
  "acceptance": [
    "P0 quests have app-card text, agent trigger, completion hint and crew fallback.",
    "No card frames quests as obligation or score.",
    "Quest/QuestRun separation remains intact."
  ],
  "checks": [
    "git diff --check"
  ],
  "humanGates": [
    "Human decides whether generated card language fits the Pax tone."
  ]
}
```

### 5.4 Ausführen

Ein Run SOLLTE:

1. Task und Spec-Kontext laden.
2. Arbeitsverzeichnis oder Worktree vorbereiten.
3. Implementer-/Worker-Prompt erzeugen.
4. Änderungen erzeugen.
5. Scope-Gate prüfen.
6. Checks ausführen.
7. Review ausführen.
8. Handoff erzeugen.
9. PR, Issue, Patch oder Artefakt für Menschen sichtbar machen.

Wenn ein Task `tdd.required` oder eine vergleichbare Testpflicht setzt, SOLLTE der Run erst eine rote Phase erzeugen und danach die Implementierung gegen die grüne Phase prüfen.

### 5.5 Review

Review DARF NICHT nur Stil oder Syntax prüfen.

Ein Reviewer MUSS prüfen:

- Wurde der erlaubte Scope eingehalten?
- Stimmen die Änderungen mit den Spec-Referenzen überein?
- Sind Akzeptanzkriterien erfüllt?
- Sind Tests, Schemata, Vektoren oder Playbook-Checks angemessen?
- Wurden neue offene Fragen sichtbar?
- Verletzt das Ergebnis Conformance, Freiwilligkeit, Sichtbarkeit oder Nicht-Ranking?

Für Code- oder Datenmodell-Änderungen SOLLTE Implementer und Reviewer nicht derselbe Agent sein.

### 5.6 Human Gate

Ein Human Gate ist erforderlich für:

- Merge in geschützte Branches,
- Release,
- normative Spec-Entscheidungen,
- Änderungen an Sicherheits-, Identitäts- oder Trust-Semantik,
- soziale Leitplanken,
- Umgang mit Kindern/Jugendlichen,
- Verarbeitung sensibler Daten,
- Änderungen, die Rollout, Crew-Arbeit oder reale Menschen direkt betreffen.

Ein Agent DARF einen Vorschlag für ein Human Gate vorbereiten, aber nicht selbst entscheiden.

### 5.7 Feedback zurückspielen

Wenn eine Implementierung oder ein Playbook eine Spec-Lücke sichtbar macht, MUSS diese Lücke sichtbar werden.

Mögliche Rückkanäle:

- offene Frage in der Spec,
- Issue,
- Follow-up-Task,
- Entscheidungslog,
- Conformance-Notiz,
- PR-Kommentar,
- Playbook-Learning.

Eine Implementierung DARF Unklarheiten lokal überbrücken, wenn der Task das erlaubt. Sie MUSS diese Interpretation dann im Handoff markieren.

## 6. Agentenrollen

| Rolle | Aufgabe | Darf nicht |
|---|---|---|
| Spec Agent | Specs schreiben, klären, strukturieren | normative Richtung ohne Human Gate setzen |
| Implementation Agent | Code, Schemata, Adapter, UI-Slices bauen | Spec-Lücken still als Code-Wahrheit setzen |
| Test Agent | Tests, Vektoren, Conformance-Cases erzeugen | Verhalten erfinden, das die Spec nicht trägt |
| Review Agent | Scope, Checks, Conformance und Risiken prüfen | Merge-Entscheidung ersetzen |
| Rollout Agent | Playbooks, Crew-Briefings, Checklisten ableiten | Menschen steuern oder Drucklogik einbauen |
| Quest Agent | Handlungseinladungen, Karten und Agenten-Trigger formulieren | Quests als Pflicht, Score oder Bewertung formulieren |
| Documentation Agent | Handoffs, Changelogs und Lernnotizen pflegen | dynamische Run-Ergebnisse als Normtext ausgeben |

Ein Agent KANN mehrere Rollen beherrschen. Ein einzelner Run SOLLTE Rollen aber explizit machen.

## 7. Zustandsmodell

Eine Umsetzung DARF eigene Statusnamen verwenden. Die folgende Mindestsemantik SOLLTE abbildbar sein:

| Status | Bedeutung |
|---|---|
| `todo` | Task ist vorbereitet, aber nicht gestartet. |
| `running` | Agent oder Runner arbeitet. |
| `fix-required` | Checks, Review oder Scope-Gate verlangen Änderungen. |
| `human-gate` | menschliche Entscheidung erforderlich. |
| `ready-for-human` | Agentische Arbeit ist abgeschlossen und bereit für menschlichen Review. |
| `completed` | Mensch hat Ergebnis akzeptiert oder Prozess bewusst abgeschlossen. |
| `cancelled` | Task wurde verworfen oder ersetzt. |

GitHub Labels, Queue-Dashboards oder lokale Event-Logs können diese Zustände spiegeln. Die maschinenlesbare Runner-State-Datei oder Queue bleibt die technische Quelle für Run-Zustand; GitHub ist die soziale Sicht für Review und Zusammenarbeit.

## 8. Handoff

Jeder nicht-triviale Run SOLLTE mit einem Handoff enden.

Ein Handoff enthält:

- Task-ID,
- betroffene Specs,
- geänderte Dateien oder erzeugte Artefakte,
- erfüllte Akzeptanzkriterien,
- ausgeführte Checks,
- Review-Ergebnis,
- bekannte Risiken,
- offene Fragen,
- benötigte Human-Gates,
- Follow-up-Vorschläge.

Der Handoff MUSS klar zwischen Ergebnis, Annahme und offener Entscheidung unterscheiden.

## 9. Conformance

Eine Agent-Workflow-Umsetzung ist `rlap-agent-workflow@0.1`-konform, wenn sie:

- Task-Scope, Spec-Referenzen und Akzeptanzkriterien explizit macht,
- Scope-Gates oder gleichwertige Prüfungen durchführt,
- menschliche Gates für Merge, Release und normative Entscheidungen respektiert,
- Agentenarbeit reviewbar macht,
- offene Fragen sichtbar zurückspielt,
- Conformance und soziale Leitplanken prüft,
- keine Menschen bewertet oder steuert,
- keine sensiblen Daten ohne Grundlage in Prompts, Logs oder PRs trägt.

## 10. Erste Anwendung auf RLN

Naheliegende erste Task-Lanes für das Real Life Network:

| Lane | Erste Slices |
|---|---|
| Pax Quest Cards | P0-Quest-Karten, Agenten-Trigger, Crew-Fallbacks |
| Vernetzungszelt | Playbook, Ablaufplan, analoge Fallbacks |
| Agentenverhalten | konkrete Erlaubnisse, Grenzen, Prompt-Kontrakte |
| Datenmodell | RLS-Item-Views für Profile, Quests, QuestRuns, Follow-ups |
| Conformance | Checklisten für Apps, Playbooks und Agenten |
| Game Boundary | klare Ableitung: Quest-Basis hier, Game-Mechaniken in [real-life-org/real-life-game](https://github.com/real-life-org/real-life-game) |

Der erste praktische Slice SOLLTE klein bleiben:

```text
Aus dem Quest-Katalog P0-App-Karten ableiten:
Titel, Kurztext, Trigger, Completion-Hinweis, Sichtbarkeit, Crew-Fallback.
```

## 11. Offene Fragen

- Welche Task-Schema-Felder sollen maschinenlesbar in `schemas/` formalisiert werden?
- Welche Agentenrollen brauchen eigene Prompts oder Systemregeln?
- Welche Human Gates sind für RLN-Spec-Arbeit immer verpflichtend?
- Wie werden sensible Praxisdaten aus Pax/Festivals für Agentenarbeit minimiert oder anonymisiert?
- Welche Runner-Zustände sollen in GitHub Labels gespiegelt werden?
- Welche Conformance-Checks können automatisiert werden, welche bleiben menschlich?
