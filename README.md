# FlowExplain: Flowchart and Explanation Generation

This repository documents the prompts, JSON response schemas and backend configuration used to create programming exercises with a reference flowchart and worked explanations. It describes [FlowExplain](https://github.com/jadsonlsouza/flowexplain) at commit [`854e638aee8df430f5f4368cd2b13ee3c9706f85`](https://github.com/jadsonlsouza/flowexplain/tree/854e638aee8df430f5f4368cd2b13ee3c9706f85).

## Generation Stages

| Stage | English prompt | Original prompt | Response format |
| --- | --- | --- | --- |
| Reference flowchart | [Mermaid generation](prompts/english/mermaid-generation.json) | [Original](prompts/original-pt/mermaid-generation.json) | [Mermaid schema](schemas/mermaid-response-format.json) |
| Flowchart blocks and structural criteria | [Block generation](prompts/english/block-generation.json) | [Original](prompts/original-pt/block-generation.json) | [Blocks schema](schemas/rubric-blocks-response-format.json) |
| Worked explanations | [Explanation generation](prompts/english/explanation-generation.json) | [Original](prompts/original-pt/explanation-generation.json) | [Generated schema example](schemas/explanations-response-format-example.json) |

Each prompt file is a JSON array of Chat Completions messages with `role` and `content`. Shared instructions are expanded in the system message. The English files are translations for readers, not the original messages sent by the application. The Portuguese originals are included for verification: the source instructs the model to produce certain learner-facing text in Brazilian Portuguese.

The `user` messages are **synthetic examples** that show the structure of the payload. Values such as `<PROBLEM_STATEMENT>` are placeholders. The real problem statement, reference code, Mermaid nodes and block IDs vary with each question. No learner data or API credentials are included.

The explanation schema is generated dynamically from the block IDs and number of reference-code lines. The included file is an example for two synthetic blocks and a two-line reference program; it is not a universal fixed schema. Application-side validation and normalization add checks beyond the response schemas.

## Question Dataset

The `questions/` directory contains the frozen question versions associated with completed evaluations:

- [Portuguese questions](questions/portuguese.json): 75 questions.

All 75 questions in this evaluated set are in Portuguese; none of the English questions has a completed evaluation. The JSON contains the problem statement, reference code, Mermaid flowchart, blocks, rubrics, explanations and any examples. See [questions/README.md](questions/README.md) for the format and selection criteria. This is a snapshot, not a live database connection.

See [Backend AI Configuration](BACKEND_AI_CONFIGURATION.md) for the model, API parameters, generation sequence and reproducibility limitations.
