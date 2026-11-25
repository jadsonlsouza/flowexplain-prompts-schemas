# FlowExplain: AI Prompts and JSON Schemas

This repository documents the AI prompts, response formats, and backend settings in FlowExplain. It is a documentation snapshot of [FlowExplain](https://github.com/jadsonlsouza/flowexplain) at commit [`854e638aee8df430f5f4368cd2b13ee3c9706f85`](https://github.com/jadsonlsouza/flowexplain/tree/854e638aee8df430f5f4368cd2b13ee3c9706f85).

## Contents

- [`prompts/english/`](prompts/english/): English translations of all 16 prompt message sets, including generation, evaluation, and legacy or repair functions.
- [`prompts/original-pt/`](prompts/original-pt/): the actual Portuguese system messages emitted by the source code, with shared instructions expanded.
- [`schemas/`](schemas/): the exact `response_format` objects used by the backend. The explanation schema is a generated example, as explained below.
- [`BACKEND_AI_CONFIGURATION.md`](BACKEND_AI_CONFIGURATION.md): model selection, API settings, token limits, call sequence, validation, and reproducibility notes.

Each prompt file is a JSON array of Chat Completions messages with `role` and `content`. The English files translate the system messages for reading and do **not** imply that these English translations were used in the original application. The original code instructs the model to produce some learner-facing content in Brazilian Portuguese; those instructions and quoted Portuguese examples remain intact in translation.

The `user` message in each file is a **synthetic example** showing the payload fields built by the application. Values such as `<PROBLEM_STATEMENT>` and `<STUDENT_CODE>` are placeholders; the real messages are populated from each request. Flowchart nodes, example blocks, code, candidate errors and line counts vary with the request. These files contain no learner submissions or API credentials.

## Prompt Index

| Prompt file | Purpose | Status in the source snapshot |
| --- | --- | --- |
| `mermaid-generation` | Reference-code flowchart | Active staged generation |
| `block-generation` | Structural criteria for nonterminal nodes | Active staged generation |
| `explanation-generation` | Worked-example explanations | Active staged generation |
| `student-flow` | Student-code flowchart and comparison | Active on request |
| `natural-language-evaluation` | Natural-language response evaluation | Active on request |
| `hint`, `block-explanation`, `final-review` | Learner feedback | Active on request |
| `rubric-upgrade`, `rubric-revision` | Rubric regeneration and candidate revision | Explicit endpoints |
| `mermaid-repair`, `block-repair`, `explanation-repair`, `legacy-exercise-repair`, `rubric-repair` | Repair builders present in the service | Not called automatically by the staged-generation pipeline in this snapshot |
| `legacy-exercise-generation` | Single-call exercise generation | Fallback for generators lacking the three staged methods |

The explanation response schema is built dynamically from the block IDs and number of lines in the reference code. `explanations-response-format-example.json` shows the generated schema for two synthetic blocks and a two-line reference program; it is **not** a universal fixed schema. The schema and application-side validation are separate: the latter may impose additional constraints, normalize generated fields, or construct terminal explanations locally.

## Reproducibility

The English translations are explanatory translations, not byte-identical prompts; use the Portuguese originals and the pinned source code to reproduce the exact system messages. Check the backend configuration document for model overrides and unresolved runtime parameters before describing a particular production run. Do not put real credentials, student responses, or evaluator data in a public fork.
