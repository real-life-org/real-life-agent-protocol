# Agent Memory and Consent

**Status:** Entwurf v0.1
**Datum:** 2026-05-09
**Scope:** Erinnerung, Sichtbarkeit und Zustimmung für Agenten im Real Life Agent Protocol.

---

## 1. Zweck

Diese Spec beschreibt, wie Agenten mit Erinnerungen, Kontext, Sichtbarkeit und Zustimmung umgehen sollen.

Agenten können nützlich sein, weil sie erinnern, Zusammenhänge erkennen und nächste Schritte vorschlagen. Genau deshalb brauchen sie klare Grenzen.

## 2. Grundprinzipien

Ein Agent MUSS:

1. sichtbar machen, wenn er sich auf gespeicherten Kontext stützt,
2. zwischen lokalem Gesprächskontext, persistenter Erinnerung und öffentlicher Dokumentation unterscheiden,
3. Sichtbarkeit respektieren,
4. sensible Informationen minimieren,
5. Zustimmung für Weitergabe oder Veröffentlichung einholen,
6. Vergessen, Korrigieren oder Einschränken ermöglichen.

Ein Agent DARF NICHT:

- private Erinnerung als öffentliches Wissen behandeln,
- aus sensiblen Daten ungefragte Schlüsse ziehen,
- Erinnerungen ohne Zweck unbegrenzt sammeln,
- Personen über verdeckte Profile oder Scoring modellieren,
- Zustimmung für einen Zweck auf andere Zwecke ausweiten.

## 3. Erinnerungsebenen

| Ebene | Bedeutung | Beispiel |
|---|---|---|
| Session Context | nur aktuelles Gespräch oder aktueller Run | "Du fragst gerade nach Pax-Quest-Karten." |
| Personal Memory | Erinnerung über eine Person mit Beziehungskontext | "Anton bevorzugt GitHub-Links in Repo-Verweisen." |
| Space Memory | geteilter Kontext eines Spaces | "Im Pax-Space gibt es ein Vernetzungszelt." |
| Artifact Memory | in Specs, Issues, PRs oder Playbooks dokumentierter Kontext | "QuestRun ist vom globalen Quest-Status getrennt." |
| Public Knowledge | öffentlich zugängliche Information | GitHub-README oder veröffentlichte Spec |

Ein Agent MUSS diese Ebenen nicht immer technisch getrennt speichern, aber er MUSS sie im Verhalten unterscheiden.

## 4. Consent Events

Zustimmung SOLLTE als Ereignis oder erkennbare Handlung modellierbar sein.

Beispiele:

- Person teilt Profilfeld in einem Space.
- Person erlaubt, dass ein Agent eine Erinnerung speichert.
- Person bittet um eine Vorstellung.
- Person veröffentlicht eine Quest.
- Person nimmt an einem QuestRun teil.
- Person erlaubt, dass ein Gespräch zusammengefasst wird.

Zustimmung MUSS zweckgebunden sein. Eine Zustimmung zur lokalen Erinnerung ist keine Zustimmung zur Veröffentlichung.

## 5. Sichtbarkeit

Agenten SOLLTEN mindestens diese Sichtbarkeitsstufen verstehen:

| Sichtbarkeit | Bedeutung |
|---|---|
| private | nur für die Person oder lokal |
| agent-local | Agent darf es für diese Beziehung erinnern |
| contacts | sichtbare Kontakte |
| space | geteilter Space |
| public | öffentlich |

Diese Stufen ersetzen nicht die Sichtbarkeitsmodelle von RLN, RLS oder WoT. Sie beschreiben, wie Agenten Vertraulichkeit interpretieren müssen.

## 6. Weitergabe

Ein Agent DARF Informationen weitergeben, wenn:

- Quelle sichtbar ist,
- Zielkontext erlaubt ist,
- Zweck passt,
- keine sensible Erweiterung entsteht,
- betroffene Personen zugestimmt haben oder die Information bereits öffentlich ist.

Bei Menschen-Vorstellungen gilt:

- Einseitiger Hinweis kann möglich sein, wenn Profile entsprechend sichtbar sind.
- Direkte Vorstellung braucht Zustimmung oder klare Opt-in-Logik.
- Sensible Gründe für ein Match dürfen nicht offengelegt werden.

## 7. Handoff und Audit

Wenn Agenten Kontext in Tasks, PRs, Playbooks oder Handoffs tragen, SOLLTEN sie minimieren:

- keine unnötigen personenbezogenen Details,
- keine privaten Gesprächsinhalte ohne Zweck,
- sensible Annahmen als unsicher markieren,
- Quellen und Sichtbarkeit nennen, wenn relevant.

## 8. Offene Fragen

- Wie wird agentische Erinnerung technisch im WoT/RLS-Kontext modelliert?
- Welche Erinnerungen sollen signiert oder attestierbar sein?
- Wie funktioniert Widerruf über mehrere Agenten hinweg?
- Welche Daten dürfen in Runner-Prompts eingebettet werden?
- Wie werden sensible Pax-/Festivaldaten anonymisiert?
