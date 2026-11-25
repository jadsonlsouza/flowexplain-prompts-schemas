# Question Dataset

This directory contains the frozen exercise records associated with completed FlowExplain evaluations:

- `portuguese.json`: 75 distinct Portuguese exercises.

The JSON object contains `language`, `exportedAt` (UTC), `source`, `selection`, `total` and an `exercises` array. Each array item is the frozen exercise snapshot assigned to a completed evaluation, including `id`, `status`, `title`, `subject`, `prompt`, `referenceCode`, `mermaid`, `blocks`, `examples`, `generationPayload` and `terminalExplanation`. Blocks contain structural rubrics, hints and canonical explanations.

The selection consists of distinct questions with at least one completed evaluation. Multiple completed evaluations of the same question are represented once; their frozen snapshots were identical. All 75 selected questions are in Portuguese; the English catalog had no completed evaluations at export time. The dataset contains no evaluator identities, sessions, votes or comments. It is a static export and will not update automatically.
