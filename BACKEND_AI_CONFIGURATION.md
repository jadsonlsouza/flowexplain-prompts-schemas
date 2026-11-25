# Backend AI Configuration

This document describes the reference-flowchart and explanation generation code in FlowExplain commit `854e638aee8df430f5f4368cd2b13ee3c9706f85`, especially `backend/flow_live/ai.py`, `generation.py`, `validation.py` and `app.py`. It describes the source configuration; it is not a verified record of every historical production request.

## Client and Model

- Provider integration: OpenAI Python SDK, using `client.chat.completions.create` (Chat Completions API).
- Dependency declaration: `openai>=1.42.0`. The source does not pin an exact SDK version.
- The service reads `OPENAI_API_KEY`, or accepts an explicitly supplied credential/client. No credential is included in this repository.
- Model selection: constructor argument, otherwise `OPENAI_MODEL`, otherwise `gpt-4o`. A deployment may override the default; this snapshot alone does not establish the model or revision used for every historical generation.
- Each structured JSON request uses a stage-specific cache key: `${OPENAI_PROMPT_CACHE_KEY}:${stage}:v1`; the prefix defaults to `flow-live`. The presence of a cache key does not establish that a particular request received a cache hit.
- The application logs usage fields when available, including input, output, total and cached prompt tokens.

## Generation Requests

The token limits below are `max_tokens` values supplied to Chat Completions. Other sampling parameters are not explicitly set in these requests.

| Stage | Temperature | Maximum output tokens | Response format |
| --- | ---: | ---: | --- |
| Mermaid flowchart | 0 | 1,600 | [Mermaid schema](schemas/mermaid-response-format.json) |
| Blocks and structural criteria | 0 | 3,600 | [Blocks schema](schemas/rubric-blocks-response-format.json) |
| Canonical explanations | 0 | 3,200 | [Dynamic schema example](schemas/explanations-response-format-example.json) |

Temperature 0 is an explicit setting, not a guarantee of identical outputs. The SDK or service may have networking or retry defaults not configured by this source.

## Processing and Validation

When the generator implements all three staged methods, the application calls flowchart generation, block generation and explanation generation sequentially. It normalizes and validates each stage, then assembles and normalizes the exercise. The final block's full reference-code answer is constructed locally rather than requested from the model during the staged explanation call. The supplied examples are retained, and the title is derived from the subject or problem statement.

Individual creation and batch import invoke the same `generate_complete_exercise` function. In this source snapshot, the staged function does not automatically call repair methods after an invalid response; it reports the failed stage.

The three files in [`schemas/`](schemas/) retain their OpenAI `response_format` wrappers and are declared with `strict: true`. The explanation schema is built from the supplied block IDs and reference-code line count; the included two-block file is one generated example. Local validation and normalization impose further checks, including on Mermaid structure, blocks and code references. Prompt instructions and schema constraints can differ; the original files preserve those differences.

## Reproducibility

The exact system instructions from this snapshot are in `prompts/original-pt/`. The English versions are translations. User messages in both directories contain synthetic values, not historical exercise inputs. The repository does not establish the model revision, timestamps, SDK version, seed, environment overrides or regeneration history of any individual historical run; those claims require retained run records.
