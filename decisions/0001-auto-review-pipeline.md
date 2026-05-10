# ADR 0001: Auto-Review-Pipeline und Risk-Tiered Merging

**Status:** Entwurf
**Datum:** 2026-05-11
**Deciders:** Anton Tranelis, Codex, Claude (Eli)
**Scope:** Build-Agent-Workflow im `wot-agent-runner` und nachgelagerte Review-Disziplin

---

## Kontext

Der aktuelle PR-Review-Loop ist Anton-zentriert. Selbst nach Cross-AI-Review im `wot-agent-runner` (z.B. Codex als implementer, Claude als reviewer) plus GitHub-Bot-Reviews (CodeRabbit, Copilot) findet ein manueller Claude-Review fast immer noch zusätzliche Findings. Anton wirkt als Vermittler in mehreren Pingpong-Runden Claude↔Codex, bevor er mit gutem Gewissen mergen kann.

Beobachtbare Symptome:

- PRs landen mit `ready-for-human`-Label im Backlog, aber Anton muss Findings selbst weitergeben.
- Codex iteriert auf Reviewer-Findings, aber **prüft seinen eigenen Output nicht vor PR-Submission**.
- Manuelle Reviewer (z.B. Claude in einer separaten Session) haben keinen Loop-Kontext und reviewen wie frische Senior-Reviewer.
- Trivial-PRs (Docs, Tests, Format) durchlaufen den vollen Loop, obwohl ihr Risiko minimal ist.
- Die WoT-Spec-Konformität wird ad-hoc durch Reviewer-Bauchgefühl geprüft statt deterministisch durch Test-Vektoren.

Eine [Recherche zu AI-PR-Review-Automatisierung (Mai 2026)](https://hamy.xyz/blog/2026-02_code-reviews-claude-subagents) zeigt:

- Cloudflare betreibt produktiv ein Multi-Agent-Review-System mit Risk-Tiering ([Blog](https://blog.cloudflare.com/ai-code-review/)).
- Hamy beschreibt den **"Review-Skill auf eigenen Output"**-Pattern als wirkungsvollsten Hebel: Builder-Agent ruft Review-Skill auf seinen Diff und iteriert bis Konvergenz, bevor er "fertig" meldet.
- Auto-Merge-Patterns sind in OSS etabliert ([Renovate](https://docs.renovatebot.com/key-concepts/automerge/), [Mergify](https://docs.mergify.com/workflow/automerge/), [Kubernetes Prow/Tide](https://github.com/istio/istio/wiki/Working-with-Prow)).
- Schlüsselfertige "Builder↔Reviewer iterieren autonom bis Konvergenz"-Tools existieren nicht von der Stange — Cloudflare hat sein System selbst gebaut.

## Problemstellung

Drei Lücken im aktuellen Setup:

1. **Implementer prüft eigenen Output nicht.** Codex submitted Code, der vom Reviewer auf Trivialitäten geprüft wird, die der Implementer selbst hätte fixen können. Erzeugt Reviewer-Volumen, das nicht nötig wäre.

2. **Kein Risk-Tiering.** Jeder PR durchläuft den vollen Loop und braucht Human Gate. Trivial-PRs binden Anton-Aufmerksamkeit ohne entsprechenden Wert.

3. **Spec-Konformität nicht deterministisch.** Crypto-, Sync- und Protocol-PRs werden ad-hoc reviewed. Eine Test-Vektoren-Suite würde diese Reviews durch deterministische CI-Checks ersetzen.

Manuelle Reviewer-Sessions ohne Loop-Kontext sind ein verwandtes, aber separates Problem (siehe ["Briefing-Bridge"](#scope-abgrenzung)). Es ist nicht Teil dieser ADR.

## Architekturentscheidungen

### Entscheidung A: Builder-Self-Review vor PR-Submission

Der Implementer (Codex oder Claude) MUSS vor Erstellung der `Agent Runner Summary` und vor Auslösung der externen Reviewer eine eigene Review-Pass auf seinen Diff durchführen.

**Details:**

- Der Self-Review verwendet dieselbe Review-Skill und denselben strukturierten JSON-Output wie der Cross-AI-Reviewer.
- Self-Review-Findings ab Severity `medium` MUSS der Implementer selbst auflösen (bis zu N Iterationen, default N=2) bevor PR-Submission.
- Self-Review-Findings ab Severity `medium`, die der Implementer für False-Positives hält, MUSS er in einer eigenen `selfReviewExclusions[]`-Section dokumentieren mit Begründung.
- Findings unterhalb `medium` (low, info) gehen in den Runner-Audit, aber blockieren nicht.
- Eskaliert N Iterationen ohne Auflösung, geht der Run nach `human-decision-required`.

**Begründung:**

- Reduziert Reviewer-Last drastisch (laut [Hamy ~80%](https://hamy.xyz/blog/2026-02_code-reviews-claude-subagents)).
- Macht Codex/Claude **rechenschaftspflichtig** für eigenen Output.
- Kompatibel mit existierender Reviewer-Schema-Logik (kein neues Format).
- Selbst-Findings + Exclusions geben downstream-Reviewern echte zusätzliche Information.

**Konsequenzen:**

- Implementer-Prompts müssen erweitert werden (Review-Skill als Pflicht-Step).
- Self-Review-Iterationen verlängern Run-Dauer pro PR; sollten aber gesamt-Pingpong reduzieren.
- Falls Implementer sich selbst "approvet" obwohl der Code Bugs hat, fängt der Cross-AI-Reviewer das wie heute schon auf — kein Sicherheitsverlust.

### Entscheidung B: Risk-Tiered Merge-Gates

PRs werden in drei Tiers klassifiziert, mit unterschiedlichen Review- und Merge-Gates:

| Tier | Kriterium | Reviewer-Setup | Human Gate |
|---|---|---|---|
| **trivial** | ≤10 changed lines, reine nicht-normative Dokumentation, Kommentar-/Typo-Fixes oder formatter-only; kein Touch auf Tests, Fixtures, CI, Schemas, Vektoren, Specs oder ADRs | Self-Review + CodeRabbit | **Auto-Merge** wenn grün |
| **standard** | ≤200 changed lines, kein Touch auf normative Spec-/Protocol-/Crypto-/Sync-/Core-/Conformance-Surfaces | Self-Review + Cross-AI + CodeRabbit | Human Click (kein Pingpong nötig) |
| **spec-relevant** | Touch auf `spec/`, `crypto/`, `sync/`, `core/`, `schemas/`, `test-vectors/`, `conformance/`, `decisions/`, CI/security config, oder >200 Zeilen | Self-Review + Cross-AI + Adversarial-Pair + CodeRabbit + Copilot | Human Click mit expliziter Sign-Off-Begründung |

**Details:**

- Tier-Bestimmung erfolgt automatisch im Runner aus `changed-files`, `loc-delta` und `allowedScope` des Tasks.
- Task-Files können `tierOverride` setzen (für ungewöhnliche Fälle); Override SOLLTE im Task begründet werden.
- Tests und Fixtures sind nur dann `trivial`, wenn sie offensichtlich rein mechanisch umformatiert wurden. Neue, gelöschte oder semantisch veränderte Tests/Fixtures sind mindestens `standard`; Conformance- oder Spec-Fixtures sind `spec-relevant`.
- Trivial-Tier nutzt das Cloudflare-["Break Glass"](https://blog.cloudflare.com/ai-code-review/)-Pattern: Auto-Merge ist Default, aber nur berechtigte Maintainer, Code-Owner oder definierte Runner-Operatoren können mit Kommentar `/break-glass <grund>` überschreiben — das löst Human Gate aus. Kommentare anderer Nutzer werden auditiert, aber nicht als Gate-Signal gewertet.
- Auto-Merge wird über Mergify (oder GitHub-native auto-merge) implementiert; Konfiguration liegt im Ziel-Repo (z.B. `.mergify.yml` in `wot-agent-runner`, später `wot-core`).

**Begründung:**

- Anton wird von Trivial-PRs entlastet.
- Spec-relevant Tier behält volle Sorgfalt (Adversarial-Pair entspricht dem `feedback_dual_review`-Pattern).
- Tier-Klassifizierung ist deterministisch und nachvollziehbar.
- Break-Glass-Pattern erlaubt autorisierten Override, ohne dass standardmäßig blockiert wird.

**Konsequenzen:**

- Mergify-Setup (oder Äquivalent) muss konfiguriert werden.
- Tier-Bestimmungs-Logik muss im Runner implementiert werden.
- Tier-Boundaries werden vermutlich nach erster Praxis-Erfahrung angepasst.
- Branch-Protection-Rules müssen Auto-Merge erlauben, was eine Konfigurationsänderung am GitHub-Repo erfordert.

### Entscheidung C: WoT-Conformance-Test-Suite als required CI

Eine kanonische Test-Vektoren-Suite für die WoT-Spec wird im `wot-spec`-Repo gepflegt und in `wot-core` (TypeScript-Implementation) als required CI-Check ausgeführt.

**Details:**

- Test-Vektoren decken: BIP39-Mnemonic-Generation, HKDF-Key-Derivation, Ed25519-Signaturen, X25519-Key-Wrap, ECIES-Round-Trip, Vouching-Chains, Membership-Updates, Attestation-Roundtrips.
- Vektoren liegen als JSON in `wot-spec/test-vectors/`; `wot-spec/conformance/manifest.json` ordnet Profile, Spec-Dokumente, Schemas und Vektor-Sektionen maschinenlesbar zu.
- `wot-core` läuft vendored Kopien dieser Vektoren in jedem CI-Run; ein neuer Vektor-Set-Release im `wot-spec` triggert (via Workflow-Bot) einen Update-PR in `wot-core`, der die Fixtures byte-identisch aktualisiert.
- Bei Spec-Änderungen, die Vektoren brechen, MUSS der `wot-spec`-PR die Vektoren mit-aktualisieren und die Begründung in der PR-Description benennen.
- Andere TypeScript-/Future-Implementations (z.B. `real-life-stack`) nutzen dieselben Vektoren.

**Begründung:**

- Macht Crypto-/Protocol-Reviews deterministisch — kein Bauchgefühl mehr nötig.
- Beschleunigt Antons WoT-Implementation-Ziel direkt: Spec-Konformität als CI-Check statt Reviewer-Aufgabe.
- Vektoren werden im Lauf der Zeit zur Single Source of Truth für Inter-Implementation-Kompatibilität (wichtig für später, wenn Drittparteien implementieren).

**Konsequenzen:**

- Initial-Effort: Vektoren müssen geschrieben werden (geschätzt 2–3 Wochen, abhängig von Scope).
- `wot-spec` braucht eine Workflow-Konvention für Vektor-Updates bei Spec-Änderungen.
- Andere Implementations müssen sich an Vektoren binden — das ist Feature (Inter-Op-Garantie), aber auch eine implizite Verpflichtung.

## Scope-Abgrenzung

**Diese ADR adressiert NICHT:**

- **Briefing-Bridge zwischen Loop und manuellen Reviewer-Sessions.** Wenn Anton eine separate Claude-Session um Review bittet, bekommt diese Session heute keinen Loop-Kontext und reviewed bei null. Lösung: Skill in Claude Code, der vor manuellen Reviews den letzten Run-State und schon adressierte Findings als Kontext-Briefing einliest. Wird als separater Skill umgesetzt, nicht als rlap-Spec-Erweiterung.
- **Metriken-Dashboard.** Wird erst sinnvoll, wenn die drei Entscheidungen oben implementiert sind und Daten produzieren.
- **Network-Agent-seitige Auto-Iteration.** Network Agents haben fundamental andere Risiko-Profile (Menschen, nicht Code). Diese ADR betrifft nur Build Agents.

## Alternativen, die verworfen wurden

- **"Mehr Reviewer hinzufügen"** (Cloudflare-Style mit 7 spezialisierten Agenten): Skaliert zu komplex für aktuelle Team-Größe. Adversarial-Pair (Codex + Claude) reicht und ist im Recherche-Konsens.
- **"Alles manuell mergen"**: Skaliert nicht mit wachsendem PR-Volumen. Verstößt gegen Antons Ziel "mehr Wirkung im echten Leben".
- **"Alles auto-mergen, retro-actively fixen"**: Verletzt das Verfassungsprinzip "Human Gate bei normativen Entscheidungen". Spec-relevant Tier MUSS Human-Sign-Off behalten.
- **"GitHub Spec-Kit als Conformance-Gate verwenden"**: Spec-Kit hilft beim Schreiben, nicht beim Verifizieren. Test-Vektoren sind dafür der etablierte Ansatz.

## Codex-Briefing

Diese Sektion enthält explizite Fragen an Codex, der die Implementation der drei Entscheidungen umsetzen wird. Sie zielt auf Antons Praxis-Wissen ab, das Codex (als aktiver Runner-Operator) besser hat als Claude (als externer Reviewer).

### Fragen an Codex zu Entscheidung A (Builder-Self-Review)

1. **Code-Komplexität:** Wie viel Code im `runner.mjs` müsste geändert werden, um den Implementer-Prompt um eine Self-Review-Phase zu erweitern? Existiert schon ein Pattern, das ähnlich funktioniert (z.B. die `maybeRunReviewerTests`-Logik)?
2. **Iterations-Modell:** Soll Self-Review eine separate Worker-Invocation sein oder eine Erweiterung des Implementer-Prompts (zwei-Phasen-Output: erst Code, dann Self-Review-JSON)?
3. **Token-Budget:** Welcher zusätzliche Token-Aufwand entsteht bei Self-Review pro PR-Durchschnitt? Lohnt sich der Pingpong-Einspar?
4. **selfReviewExclusions:** Wie integriert sich das in das existierende `findingResolutions`-Schema? Sollten Exclusions als spezielle Finding-Resolution oder als separates Feld auftauchen?
5. **Edge Case:** Wenn der Implementer in Self-Review weitere Implementer-Calls braucht (z.B. um eigenen Bug zu fixen), wie wird das gegen `maxFixIterations` gezählt?

### Fragen an Codex zu Entscheidung B (Risk-Tiered Merge)

1. **Tier-Logik im Runner:** Wo ist der natürliche Ort im Runner, um Tier zu bestimmen — als Teil von `scope-gate` oder als eigene Phase?
2. **`changed-files`-Quelle:** Nutzt der Runner `git diff --name-only` oder gibt es ein zuverlässigeres Signal im worktree-Setup?
3. **Mergify vs. GitHub native auto-merge:** Was ist deine Empfehlung für ein Open-Source-Projekt unserer Größe? Gibt es Setup-Komplexität, die wir unterschätzen?
4. **Branch-Protection-Interaktion:** Wenn Auto-Merge aktiv ist, müssen branch-protection-rules angepasst werden? Welche minimalen Required-Checks würdest du setzen?
5. **Trivial-Tier-Risiken:** Welche Edge Cases siehst du, in denen ein "trivialer" PR doch problematisch sein könnte (z.B. CI-Konfig-Änderung in einer `.md`-Datei)?

### Fragen an Codex zu Entscheidung C (Conformance Test Suite)

1. **Vektor-Format:** Was wäre ein gutes Vektor-Format für die WoT-Spec — angelehnt an [W3C Test Vectors](https://www.w3.org/Privacy/), [CFRG Test Vectors](https://www.rfc-editor.org/rfc/rfc8032#section-7), oder ein eigener Stil?
2. **Cross-Repo-Workflow:** Wenn `wot-spec` Vektoren updated, wie triggern wir automatisch einen Update-PR in `wot-core` und `real-life-stack`? Gibt es einen GitHub-Actions-Pattern dafür?
3. **Coverage:** Welche Spec-Sektionen sind heute schon kanonisch genug, dass Vektoren sofort schreibbar wären — und welche sind noch zu unspezifiziert?
4. **Adapter-Abstraktion:** Wie integrieren wir Conformance-Tests in `wot-core` so, dass auch andere Crypto-/Storage-Adapter (z.B. ein zukünftiger Rust-Port) sie nutzen können?

### Allgemeine Fragen an Codex

1. **Reihenfolge:** Aus Runner-Sicht — welche der drei Entscheidungen ist am einfachsten umsetzbar und sollte zuerst kommen?
2. **Risiken:** Welche Schwachstellen im Runner-Code würden durch diese Änderungen freigelegt werden (z.B. State-Machine-Probleme, race conditions)?
3. **Backwards Compatibility:** Brechen die Änderungen existierende Tasks? Was muss in Existing Task-Files geändert werden?
4. **Test-Strategie:** Welche Tests im Runner müssten geschrieben/erweitert werden, um die drei Entscheidungen abzudecken?

## Nächste Schritte

1. **Diese ADR mit Anton + Codex reviewen**, Anpassungen einarbeiten.
2. **Drei rlap-Tasks erstellen** (im `real-life-agent-protocol`-Repo), die die drei Entscheidungen in konkrete Implementations-Slices übersetzen:
   - Task 2: `runner-builder-self-review`
   - Task 3: `runner-risk-tiered-merge`
   - Task 4: `wot-spec-conformance-vectors`
3. **Briefing-Skill für manuelle Reviews** parallel als Claude-Code-Skill umsetzen (außerhalb dieser ADR).
4. **Codex eine Run-Iteration nutzen, um die Codex-Briefing-Fragen zu beantworten.** Antworten fließen entweder als ADR-Update oder direkt in die Task-Specs ein.
5. **Nach Implementation: Metriken sammeln** (Pingpong-Rate, Auto-Merge-Rate, Time-to-Ready, Cost-per-PR), nach 4-6 Wochen Praxis bewerten und ggf. anpassen.

## Quellen

- [Orchestrating AI Code Review at scale — Cloudflare Blog](https://blog.cloudflare.com/ai-code-review/)
- [9 Parallel AI Agents That Review My Code — Hamy](https://hamy.xyz/blog/2026-02_code-reviews-claude-subagents)
- [The Architecture of Agentic Code Review — Baz](https://baz.co/resources/engineering-intuition-at-scale-the-architecture-of-agentic-code-review)
- [Renovate Automerge Docs](https://docs.renovatebot.com/key-concepts/automerge/)
- [Mergify Auto-Merge Docs](https://docs.mergify.com/workflow/automerge/)
- [Istio Working with Prow](https://github.com/istio/istio/wiki/Working-with-Prow)
- [10 Best AI Code Review Tools 2026 — CodeAnt](https://www.codeant.ai/blogs/best-ai-code-review-tools)
- [GitHub Spec-Kit](https://github.com/github/spec-kit)
- [Specmatic — Spec-driven Testing](https://specmatic.io/)
