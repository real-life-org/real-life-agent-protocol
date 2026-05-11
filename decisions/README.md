# Architecture Decision Records (ADRs)

Dieses Verzeichnis enthält Architekturentscheidungen für das Real Life Agent Protocol und den zugehörigen Workflow.

ADRs dokumentieren **Warum** wir etwas so entschieden haben, im Gegensatz zu Specs, die das **Was** beschreiben.

## Format

Jede ADR folgt dieser Struktur:

- **Status**: Entwurf / Akzeptiert / Abgelöst durch ADR-XXXX / Verworfen
- **Datum**: Datum der Entscheidung oder des letzten Updates
- **Deciders**: Wer an der Entscheidung beteiligt war
- **Scope**: Welche Bereiche/Repos betroffen sind
- **Kontext**: Welches Problem zu lösen war
- **Architekturentscheidungen**: Was entschieden wurde, mit Begründung und Konsequenzen
- **Alternativen**: Was verworfen wurde und warum
- **Nächste Schritte**: Was als Folge umgesetzt werden muss

## Numerierung

ADRs sind sequenziell numeriert. Eine ADR wird **nicht** rückwirkend geändert — wenn sich die Entscheidung ändert, wird eine neue ADR geschrieben, die die alte ablöst (Status: "Abgelöst durch ADR-XXXX").

## Liste

- [0001 — Auto-Review-Pipeline und Risk-Tiered Merging](./0001-auto-review-pipeline.md)
