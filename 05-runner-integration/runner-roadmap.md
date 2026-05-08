# Runner Roadmap

**Status:** Entwurf v0.1
**Datum:** 2026-05-09
**Scope:** Priorisierte Weiterentwicklung des [real-life-org/wot-agent-runner](https://github.com/real-life-org/wot-agent-runner) auf Basis von `rlap-agent-workflow@0.1`.

---

## 1. Zweck

Diese Roadmap sammelt konkrete Runner-Verbesserungen aus der laufenden WoT-Referenzimplementierungsarbeit.

Sie ist bewusst implementierungsnah. Sie erweitert nicht das allgemeine Agent Protocol, sondern beschreibt, wie der bestehende Runner stabiler und besser RLAP-konform werden kann.

## 2. Aktuelle Bewertung

Der Runner macht grundsätzlich Sinn.

Das Kernmuster passt:

```text
Task-Datei
  -> isolierter Worktree
  -> Implementer
  -> Reviewer
  -> PR
  -> Human Gate
```

Die größten Reibungsverluste liegen aktuell im Review-/Status-Loop:

- Review-Threads werden nicht sauber genug nach aktuell/stale/outdated unterschieden.
- Labels wie `agent:fix-required` können hängen bleiben.
- Attach-/Refresh-Runs brauchen feinere Modi.
- Review-Prompts können zu groß werden.
- Status-Reporting muss lokale Checks, GitHub Reviews und Thread-Zustand besser trennen.

## 3. P0

P0-Punkte sind Stabilitäts- und Korrektheitsarbeit am bestehenden Runner.

### 3.1 Aktuelle Review-Blocker korrekt bestimmen

Der Runner MUSS unterscheiden:

- aktuelle, nicht-outdated Review-Threads,
- echte `CHANGES_REQUESTED`,
- stale/outdated Bot-Kommentare,
- bereits resolved Threads,
- Issue-Kommentare ohne konkreten Code-Blocker.

Nur nicht-outdated Review-Threads oder echte `CHANGES_REQUESTED` SOLLTEN blockieren.

Outdated CodeRabbit-/Copilot-Kommentare SOLLTEN ignoriert oder separat als `stale` markiert werden.

### 3.2 Attach/Refresh ohne neue Review-Anforderung

Der Runner SOLLTE existierende PRs refreshen können, ohne neue GitHub Reviews anzufordern.

Beispiel:

```bash
node src/runner.mjs \
  --attach-pr <url> \
  --no-request-github-reviews
```

Dieser Modus SOLLTE:

- bestehenden PR und Worktree wieder aufnehmen,
- Checks und Thread-Zustand neu erfassen,
- Summary und Labels aktualisieren,
- keine neue Copilot-/CodeRabbit-Review anfordern.

### 3.3 `agent:fix-required` automatisch bereinigen

Wenn alle aktuellen Threads resolved sind und Checks grün sind, SOLLTE der Runner `agent:fix-required` automatisch entfernen oder durch den passenden Status ersetzen.

Beispiele:

- `agent:ready-for-human`,
- `agent:human-gate`,
- `agent:blocked`.

### 3.4 Prompts nicht als CLI-Argument übergeben

Reviewer- und Implementer-Prompts SOLLTEN über stdin oder temporäre Dateien übergeben werden, nicht als lange CLI-Argumente.

Begründung:

- große Diffs und Audit-Pakete können `argument list too long` auslösen,
- Claude/Codex CLI bleiben robuster,
- Prompt-Artefakte werden auditierbarer.

## 4. P1

P1-Punkte verbessern Effizienz, Statusklarheit und Review-Qualität.

### 4.1 Fix-Iteration ohne automatische neue GitHub Reviews

Eine Fix-Iteration SOLLTE nicht automatisch neue komplette GitHub Reviews anfordern.

Empfohlener Ablauf:

1. lokal fixen,
2. pushen,
3. adressierte Threads resolven,
4. Summary aktualisieren,
5. neue GitHub Review nur explizit anfordern.

### 4.2 CodeRabbit-Issue-Kommentare klassifizieren

CodeRabbit-Issue-Kommentare SOLLTEN klassifiziert werden:

| Klasse | Bedeutung |
|---|---|
| `blocker` | aktuelle, konkrete Änderung erforderlich |
| `suggestion` | sinnvoll, aber nicht blockierend |
| `nit` | Kleinigkeit |
| `stale` | durch neue Commits/outdated erledigt |
| `out-of-scope` | passt nicht zum Task-Scope |

### 4.3 Runner Summary differenzieren

Die Runner Summary SOLLTE getrennt ausweisen:

- lokale Checks grün/rot,
- GitHub Reviews requested/commented/changes-requested,
- unresolved aktuelle Threads,
- stale/outdated Kommentare,
- Human Gates,
- Residual Risk.

### 4.4 Worker-Fallback dokumentieren

Wenn der Runner von Claude auf Codex oder umgekehrt fällt, MUSS der Handoff das dokumentieren.

Wenn `--allow-same-worker-review` genutzt wird, MUSS das sichtbar sein.

### 4.5 Review Prompt verkleinern

Review-Prompts SOLLTEN nur enthalten:

- Task,
- Spec-Refs,
- relevante Diffs,
- Check-Ergebnisse,
- offene aktuelle Threads,
- relevante Handoff-/Audit-Zusammenfassung.

Sie SOLLTEN nicht bei jeder Review das gesamte Kontextpaket erneut mitsenden.

## 5. P2

P2-Punkte verbessern Bedienbarkeit und Automatisierung.

### 5.1 Task-Komfortkommando

Der Runner SOLLTE Komfortkommandos für Task-Dateien anbieten.

Beispiele:

```bash
node src/runner.mjs task validate tasks/foo.json
node src/runner.mjs task import plans/program.json
```

### 5.2 Resume/Attach robuster machen

Der Runner SOLLTE bestehende PRs anhand von Task-ID, Branch oder PR-URL finden und fortsetzen können.

Ziel:

- weniger manuelles Attach-Mapping,
- weniger verlorene Run-Kontexte,
- bessere Fortsetzung nach menschlichen Merges oder Dirty-PRs.

### 5.3 Spec-Issue-Helfer

Der Runner KANN aus einem Ambiguity-Block einen vorbereiteten Issue-Body erzeugen.

Beispiel:

```bash
node src/runner.mjs issue draft --from runs/<run-id>/handoff.md
```

Optional kann daraus später ein kontrolliertes `gh issue create` werden, aber nur mit expliziter Bestätigung.

## 6. Verhältnis zur Ambiguity Policy

Der Spec-Issue-Helfer DARF nur auf Basis der Ambiguity Policy arbeiten.

Er MUSS unterscheiden:

- Spec Ambiguity,
- Domain Mapping,
- Implementation Design.

Er DARF kein Issue für reine Domain-Mapping-Fragen erstellen, wenn vorhandene Protokollkonzepte ausreichen.

## 7. Offene Fragen

- Welche GitHub-Thread-Daten reichen aus, um stale/outdated sicher zu klassifizieren?
- Soll `agent:fix-required` von mehreren Quellen getrennt werden, z.B. `fix:checks`, `fix:review`, `fix:scope`?
- Wie wird ein Review-Bot-Kommentar als out-of-scope markiert?
- Welche Prompt-Artefakte dürfen aus Datenschutzsicht dauerhaft im Run-Verzeichnis bleiben?
- Soll der Runner mehrere Summary-Views erzeugen: kurz für PR, lang für Audit?
