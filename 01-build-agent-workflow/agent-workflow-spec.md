# Agent Workflow Spec

**Status:** Entwurf v0.1
**Datum:** 2026-05-09
**Scope:** Arbeitsprotokoll für KI-Agenten, die aus Specs Software, Tests, Playbooks, Quests, Rollout-Artefakte oder Spec-Verbesserungen ableiten.

---

## 1. Zweck

Diese Spec beschreibt, wie KI-Agenten spec-konform am Aufbau des Real Life Networks und der zugehörigen Software arbeiten können.

Sie ist keine Protokoll-Spec im Sinne von Wire-Formaten oder Identitätssemantik. Sie ist ein operativer Workflow Contract: Sie beschreibt, wie Menschen, Runner, Agenten und Review-Systeme zusammenarbeiten, welche Zustände gelten und wann ein Agent stoppen muss.

Sie verallgemeinert das Muster, das bereits mit [real-life-org/wot-spec](https://github.com/real-life-org/wot-spec) und [real-life-org/wot-agent-runner](https://github.com/real-life-org/wot-agent-runner) entsteht:

```text
Vision -> Spec -> Task -> Agentenarbeit -> PR/Artefakt -> Review -> Human Gate -> Feedback in Spec
```

Das Ziel ist nicht, Menschen aus dem Prozess zu entfernen. Das Ziel ist, Agenten so einzubetten, dass sie klar begrenzte, prüfbare Arbeit leisten können, ohne die soziale, technische oder normative Richtung selbst zu setzen.

Der Runner ist dabei nicht nur ein Script-Starter. Er ist die operative Steuerzentrale für Task-Zustand, Worktree, Prompt-Erzeugung, Checks, Audit-Artefakte, PR-Handoff, Review-Zustand und Human Gates.

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
| Spec Ambiguity | Protokoll ist widersprüchlich, unvollständig, testvektorlos oder in Wire-/Conformance-Semantik unklar. |
| Domain Mapping | Fachliche Begriffe werden auf vorhandene Protokollkonzepte gemappt, ohne dass das Protokoll geändert werden muss. |
| Implementation Design | Architektur-, Adapter-, Persistence-, UX- oder Runtime-Entscheidung in einer konkreten Implementierung. |

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
| Program Operator | Programm planen, Tasks schneiden, Queue priorisieren, Human Gates koordinieren | Agentenarbeit ungeprüft als erledigt behandeln |
| Spec Agent | Specs schreiben, klären, strukturieren | normative Richtung ohne Human Gate setzen |
| Implementation Agent | Code, Schemata, Adapter, UI-Slices bauen | Spec-Lücken still als Code-Wahrheit setzen |
| Test Agent | Tests, Vektoren, Conformance-Cases erzeugen | Verhalten erfinden, das die Spec nicht trägt |
| Review Agent | Scope, Checks, Conformance und Risiken prüfen | Merge-Entscheidung ersetzen |
| GitHub Review Bot | additive PR-Review-Kommentare liefern | lokale Cross-AI-Review oder menschliche Entscheidung ersetzen |
| Human Maintainer | Merge, Release, normative Entscheidungen und Prioritäten entscheiden | operative Run-Artefakte ignorieren, wenn sie Blocker zeigen |
| Rollout Agent | Playbooks, Crew-Briefings, Checklisten ableiten | Menschen steuern oder Drucklogik einbauen |
| Quest Agent | Handlungseinladungen, Karten und Agenten-Trigger formulieren | Quests als Pflicht, Score oder Bewertung formulieren |
| Documentation Agent | Handoffs, Changelogs und Lernnotizen pflegen | dynamische Run-Ergebnisse als Normtext ausgeben |

Ein Agent KANN mehrere Rollen beherrschen. Ein einzelner Run SOLLTE Rollen aber explizit machen.

## 7. Artefakte

Ein Agent Workflow SOLLTE die folgenden Artefakte unterscheiden:

| Artefakt | Zweck |
|---|---|
| Program Plan | Langfristige Landkarte, Phasen, Conformance-Ziele und offene Entscheidungen. |
| Task JSON | Kleine, ausführbare Beschreibung eines Slice mit Scope, Checks und Gates. |
| Run Worktree | isolierter Arbeitsbereich des Runs, nicht der Hauptcheckout. |
| Prompt Artifact | erzeugter Prompt für Implementer, Reviewer oder Fixer. |
| Audit Artifact | Diff, Scope-Gate, Checks, Review und Runner-State. |
| Pull Request | sichtbarer Review- und Integrationsort. |
| Agent Runner Summary | dynamischer Statuskommentar mit Run-ID, Checks, Review und Risiken. |
| Spec Issue | explizite Protokollfrage oder normative Lücke. |
| Boundary/Conformance Doc | Dokumentiert Architekturgrenzen, Legacy-Grenzen oder Conformance-Abdeckung. |

Ein Run DARF dynamische Run-Ergebnisse nicht als normative Spec ausgeben. Normative Änderungen brauchen Spec-PR oder Human Gate.

## 8. Zustandsmodell

Eine Umsetzung DARF eigene Statusnamen verwenden. Die folgende Mindestsemantik SOLLTE abbildbar sein:

| Status | Bedeutung |
|---|---|
| `proposed` | Task-Idee existiert, ist aber noch nicht Definition-of-Ready. |
| `queued` / `todo` | Task ist vorbereitet und kann gestartet werden. |
| `running` | Agent oder Runner arbeitet. |
| `checks-failed` | konfigurierte Checks sind fehlgeschlagen. |
| `fix-required` | Checks, Review oder Scope-Gate verlangen Änderungen. |
| `ready-for-human` | Agentische Arbeit ist abgeschlossen und bereit für menschlichen Review. |
| `human-gate` | menschliche Entscheidung erforderlich. |
| `merged` | PR wurde menschlich gemerged. |
| `superseded` | Task wurde durch neuen Task oder Batch ersetzt. |
| `closed` / `cancelled` | Task oder PR wurde bewusst geschlossen. |
| `completed` | Prozess ist abgeschlossen, z.B. nach Merge oder bewusst akzeptiertem Artefakt. |

GitHub Labels, Queue-Dashboards oder lokale Event-Logs können diese Zustände spiegeln. Die maschinenlesbare Runner-State-Datei oder Queue bleibt die technische Quelle für Run-Zustand; GitHub ist die soziale Sicht für Review und Zusammenarbeit.

## 9. Definition of Ready

Ein Task ist Definition-of-Ready, wenn mindestens gilt:

- Spec-Scope ist eindeutig.
- betroffene Dateien oder Module sind konkret benannt.
- `allowedScope[]` und `forbiddenScope[]` sind gesetzt.
- Checks sind angegeben.
- Akzeptanzkriterien sind prüfbar.
- bekannte Spec-Issues sind verlinkt.
- Human Gates sind benannt.
- der Task ist klein genug für einen reviewbaren PR.

Ein Task ist NICHT ready, wenn er mehrere unabhängige Architekturentscheidungen, große Refactors und neue Protokollsemantik in einem PR mischt.

## 10. Definition of Done

Ein agentischer Task ist nicht durch "Agent ist fertig" erledigt.

Definition of Done für einen PR-Task:

- konfigurierte Checks sind grün oder dokumentiert bewusst nicht ausführbar.
- Scope-Gate ist bestanden.
- lokaler Review ist abgeschlossen.
- aktuelle GitHub-Review-Kommentare auf dem Head sind gelöst oder als Human Gate dokumentiert.
- stale/outdated Review-Kommentare sind nicht als aktuelle Blocker gezählt.
- PR-Labels spiegeln den aktuellen Zustand.
- Agent Runner Summary enthält Run-ID, Checks, Issues und Residual Risk.
- Review Finding Coverage ist ausgewiesen, wenn vorherige lokale, GitHub- oder menschliche Findings adressiert oder klassifiziert wurden.
- PR bleibt ohne menschliche Entscheidung ungemerged.

Für Spec- oder Playbook-Tasks gilt entsprechend: Artefakt ist sichtbar, Review ist abgeschlossen, offene Fragen sind markiert und normative Entscheidungen sind nicht automatisiert worden.

## 11. Ambiguity Policy

Agenten MÜSSEN zwischen Spec Ambiguity, Domain Mapping und Implementation Design unterscheiden.

| Kategorie | Bedeutung | Richtiger Kanal |
|---|---|---|
| Spec Ambiguity | Protokoll ist widersprüchlich, unvollständig, testvektorlos oder Wire-/Conformance-Semantik ist unklar. | Spec Issue oder Spec PR |
| Domain Mapping | Fachlicher Kontext kann auf vorhandene Protokollkonzepte gemappt werden. | Task-Handoff, Kommentar oder Domain-Doc |
| Implementation Design | Ports, Adapter, Demo, Persistence, UX oder Runtime sind zu entscheiden. | Implementation Task, ADR, PR oder Boundary Doc |

Ein Agent SOLLTE ein neues Spec Issue erstellen oder vorschlagen, wenn:

- zwei normative Aussagen widersprechen,
- ein Wire-Format oder signiertes Format unklar ist,
- ein Conformance-Profil nicht testbar ist,
- Testvektoren, Schemata oder Validierungsregeln fehlen,
- Implementierungen ohne Spec-Entscheidung inkompatibel würden.

Ein Agent SOLLTE KEIN Spec Issue erstellen, wenn:

- ein fachlicher Begriff bereits auf vorhandene Konzepte gemappt werden kann,
- eine App-Projektion oder UI-Entscheidung offen ist,
- ein Adapter- oder Persistence-Design fehlt,
- der Kontext lokal durch Task-Akzeptanzkriterien geklärt werden kann.

Beispiel:

```text
Space-Sichtbarkeit reicht als Protokollkonzept.
Region ist Filter, Ort oder Kontext.
Das ist Domain Mapping, keine neue Sichtbarkeitsstufe und keine Spec-Ambiguity.
```

Bei Unsicherheit DARF ein Agent `[NEEDS CLARIFICATION]` im Handoff verwenden. Er SOLLTE dabei angeben, ob die Klärung wahrscheinlich Spec, Domain Mapping oder Implementation Design betrifft.

## 12. Merge- und Batch-Policy

Der Runner kann einzelne Slices gut bearbeiten. Programme brauchen zusätzlich Merge-Disziplin.

Ein Program Operator SOLLTE:

- Core-PRs vor Extension-PRs mergen.
- saubere PRs vor dirty PRs priorisieren.
- große Merge-Batches vermeiden.
- nach jedem Batch die Zielbranch neu prüfen.
- keine neuen Implementierungs-PRs auf alter Base starten, wenn viele Basis-PRs bereits dirty sind.
- dirty PRs rebased, superseded oder geschlossen bekommen, statt Merge-Schulden unbegrenzt wachsen zu lassen.

Ein Agent DARF einen Rebase- oder Supersede-Vorschlag machen. Er DARF nicht selbst entscheiden, welche konkurrierende fachliche Richtung gewinnt.

## 13. Concurrency Rules

Agentenarbeit darf parallel laufen, aber nicht blind.

Regeln:

- Ein Agent DARF weiter analysieren, während ein Mensch parallel merged.
- Ein Agent SOLLTE keine neue Code-PR-Lane starten, wenn viele Basis-PRs offen, dirty oder ungeordnet sind.
- Neue Tasks SOLLTEN auf aktueller Base starten.
- Bei Dirty-PRs SOLLTE zuerst konsolidiert, rebased oder superseded werden.
- Program-Fortschritt MUSS aus PRs, Checks, Coverage, Docs und offenen Human Gates zusammengeführt werden; Prozentfortschritt ist kein einzelner Runner-State.

## 14. Review Rules

Review ist mehrstufig:

- Implementer und lokaler Reviewer SOLLTEN unterschiedliche Agenten oder Modelle sein.
- GitHub Review Bots wie Copilot oder CodeRabbit sind additiv.
- aktuelle Review-Kommentare auf dem PR-Head zählen als Blocker.
- stale, outdated oder bereits gelöste Review-Kommentare zählen nicht als aktuelle Blocker.
- späte Review-Kommentare können `ready-for-human` wieder zu `fix-required` machen.
- Reviewer dürfen zusätzliche Checks nur aus einer erlaubten Command-Liste anfordern.
- fehlende oder unstrukturierte Review-Ausgabe SOLLTE ein Human Gate erzeugen.
- adressierte Findings SOLLTEN nicht nur als "resolved" markiert werden, sondern mit Evidence oder Begründung im Handoff sichtbar bleiben.

Ein Review MUSS zwischen echten Bugs, Conformance-Risiken, Stilfragen und fachlichen Klärungen unterscheiden.

### 14.1 Review Finding Coverage

Wenn ein Finding aus lokaler Review, GitHub Review, Review Bot oder menschlichem Review adressiert wird, SOLLTE der nächste Review-/Handoff-Zustand eine strukturierte `findingResolution` oder gleichwertige Evidenz enthalten.

Zulässige Resolution-Klassen:

| Status | Bedeutung | Erforderliche Evidenz |
|---|---|---|
| `fixed` | Finding wurde durch Code, Spec, Test oder Dokumentation behoben. | `regressionTest` oder `noTestReason` |
| `not-applicable` | Finding trifft auf den aktuellen Head oder Scope nicht zu. | `notes` mit Begründung |
| `needs-human` | Finding ist keine reine Implementierungsfrage. | `notes` mit Entscheidungskontext |

Ein Runner SOLLTE structured review output als ungültig behandeln, wenn eine `fixed`-Resolution weder Testevidenz noch `noTestReason` enthält. Für `not-applicable` und `needs-human` MUSS die Begründung menschlich lesbar sein; reine Thread-Resolution reicht nicht.

Ein Handoff SOLLTE getrennt ausweisen:

- offene Findings,
- behobene Findings mit Evidence,
- bewusst nicht anwendbare Findings,
- Findings, die ein Human Gate brauchen.

### 14.2 GitHub Review Thread Semantik

GitHub REST-Kommentare und GraphQL Review Threads können unterschiedliche Aktualitätssignale liefern. Eine Umsetzung SOLLTE aktuelle unresolved Review Threads als maßgeblich behandeln, wenn REST-Kommentardaten allein den Zustand nicht sicher erklären.

Mindestregeln:

- unresolved, nicht-outdated automatisierte Review Threads auf dem aktuellen Head blockieren `ready-for-human`.
- resolved oder outdated Threads blockieren nicht.
- reine Status-/Summary-Issue-Kommentare von Bots blockieren nicht ohne konkretes Review-Finding.
- vor einem finalen `ready-for-human` SOLLTE ein frischer Review-Snapshot eingeholt werden.

### 14.3 Attach/Refresh Runs

Ein Runner SOLLTE bestehende PRs refreshen können, ohne neue externe Reviews anzufordern.

Ein Attach/Refresh Run SOLLTE:

- Implementierung überspringen können,
- konfigurierte Checks erneut ausführen,
- aktuellen GitHub Review-/Thread-Zustand sammeln,
- Summary und Labels aktualisieren,
- keine neue Copilot-/CodeRabbit-Review anfordern, wenn dies nicht explizit gewünscht ist.

Attach/Refresh ist kein Merge- oder Rebase-Modus. Er dient Statusklarheit und Handoff-Aktualisierung.

## 15. Betriebsrisiken und Gegenmaßnahmen

| Risiko | Gegenmaßnahme |
|---|---|
| Zu viele offene PRs erzeugen Merge-Schulden. | Batch-Größe begrenzen, dirty PRs konsolidieren, Program Operator einsetzen. |
| Dependency-Reihenfolge ist unklar. | Program Plan und Task-Abhängigkeiten pflegen. |
| Async Reviews kommen spät. | Finalen Review-Snapshot vor `ready-for-human` nutzen. |
| Auto-Fix-Prompts werden zu groß. | Fix-Scope klein halten, Findings bündeln, große Aufgaben splitten. |
| Runner kann Slices, aber kein ganzes Programm konsolidieren. | Fortschritts- und Boundary-Docs als eigene Tasks führen. |
| Agenten melden zu viele Spec Issues. | Ambiguity Policy anwenden. |
| Findings werden als "resolved" behandelt, ohne Regression oder Begründung. | Review Finding Coverage verlangen. |

## 16. Handoff und Reporting

Jeder nicht-triviale Run SOLLTE mit einem Handoff enden.

Ein Handoff enthält:

- Task-ID,
- Run-ID,
- PR-URL, falls vorhanden,
- Commit(s), falls vorhanden,
- betroffene Specs,
- geänderte Dateien oder erzeugte Artefakte,
- erfüllte Akzeptanzkriterien,
- ausgeführte Checks,
- Review-Ergebnis,
- Review Finding Coverage,
- Issues und Spec-Fragen,
- Blocker,
- bekannte Risiken,
- Residual Risk,
- benötigte Human-Gates,
- Follow-up-Vorschläge.

Der Handoff MUSS klar zwischen Ergebnis, Annahme und offener Entscheidung unterscheiden.

## 17. Conformance

Eine Agent-Workflow-Umsetzung ist `rlap-agent-workflow@0.1`-konform, wenn sie:

- Task-Scope, Spec-Referenzen und Akzeptanzkriterien explizit macht,
- Definition of Ready und Definition of Done abbildet,
- Scope-Gates oder gleichwertige Prüfungen durchführt,
- menschliche Gates für Merge, Release und normative Entscheidungen respektiert,
- Agentenarbeit reviewbar macht,
- Spec Ambiguity, Domain Mapping und Implementation Design unterscheidet,
- offene Fragen sichtbar zurückspielt,
- Conformance und soziale Leitplanken prüft,
- keine Menschen bewertet oder steuert,
- keine sensiblen Daten ohne Grundlage in Prompts, Logs oder PRs trägt.

## 18. Erste Anwendung auf RLN

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

## 19. Offene Fragen

- Welche Task-Schema-Felder sollen maschinenlesbar in `schemas/` formalisiert werden?
- Welche Agentenrollen brauchen eigene Prompts oder Systemregeln?
- Welche Human Gates sind für RLN-Spec-Arbeit immer verpflichtend?
- Wie werden sensible Praxisdaten aus Pax/Festivals für Agentenarbeit minimiert oder anonymisiert?
- Welche Runner-Zustände sollen in GitHub Labels gespiegelt werden?
- Welche Conformance-Checks können automatisiert werden, welche bleiben menschlich?
- Wie werden Task-Abhängigkeiten und Batch-Reihenfolgen maschinenlesbar modelliert?
- Wie wird Program-Fortschritt aus PRs, Checks, Docs, Coverage und Human Gates aggregiert?
