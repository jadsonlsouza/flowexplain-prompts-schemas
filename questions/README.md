# Question Dataset

This directory contains a read-only snapshot exported from the FlowExplain exercise API. The files are separated by the exercise language:

- `portuguese.json`: 140 Portuguese exercises.
- `english.json`: 6 English exercises.

Each file is a JSON object with `language`, `exportedAt` (UTC), `source`, `total` and an `exercises` array. Each array item is the corresponding full exercise returned by the API, including `id`, `status`, `title`, `subject`, `prompt`, `referenceCode`, `mermaid`, `blocks`, `examples`, `generationPayload`, and, when present, `terminalExplanation`. Blocks can contain structural rubrics, hints and canonical explanations. The API may normalize Mermaid or infer nodes when serializing records, so the export represents the API response rather than raw database tables.

All exercises in this snapshot were approved at export time. It contains no evaluator sessions, votes or comments. Question IDs and the Portuguese originals are preserved; the English copies are distinct exercises. The snapshot will not update automatically when the application database changes.
