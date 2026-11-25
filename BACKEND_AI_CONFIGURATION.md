# Backend AI Configuration

This document describes the code in FlowExplain commit `854e638aee8df430f5f4368cd2b13ee3c9706f85`, particularly `backend/flow_live/ai.py`, `generation.py`, `validation.py`, and `app.py`. It describes source configuration, not a verified record of every historical production request.

## Client and Model

- Provider integration: OpenAI Python SDK, `client.chat.completions.create` (Chat Completions API).
- Dependency declaration: `openai>=1.42.0`; the source does not pin a single SDK version.
- The service reads its API credential from `OPENAI_API_KEY`, or accepts an explicitly supplied credential/client. No credential is included here.
- Model: constructor argument, otherwise `OPENAI_MODEL`, otherwise `gpt-4o`. The value in a deployment can override the default; this source snapshot alone does not establish the model or revision used for every past generation.
- Structured JSON calls use a stage-specific cache key: `${OPENAI_PROMPT_CACHE_KEY}:${stage}:v1`; the prefix defaults to `flow-live`. Hint, block explanation, and final-review calls do not use this helper.
- The code logs available usage metrics, including input, output, total and cached prompt tokens. A cache key does not establish that a request received a cache hit.

## Request Settings

The limits below are `max_tokens` values passed to Chat Completions. `temperature` is explicitly set as shown. Other sampling parameters are not explicitly set in these calls.

| Operation | Temperature | Maximum output tokens | Response format |
| --- | ---: | ---: | --- |
| Mermaid generation | 0 | 1,600 | `mermaid-response-format.json` |
| Structural blocks and rubric generation | 0 | 3,600 | `rubric-blocks-response-format.json` |
| Canonical explanations | 0 | 3,200 | Dynamic explanations schema |
| Legacy all-in-one exercise generation | 0 | 3,600 | JSON object mode |
| Mermaid / block / explanation / legacy repair methods | 0 | Respective stage limit | Respective stage format |
| Rubric upgrade, repair and revision | 0 | 3,600 | Rubric blocks schema |
| Learner hint | 0.4 | 220 | Plain text |
| Explanation of selected block | 0.2 | 90 | Plain text |
| Final review | 0.2 | 700 | Plain text |
| Student flowchart and comparison | 0.2 | 1,800 | Student flow schema |
| Natural-language evaluation | 0.2 | 1,600 | Natural-language schema |

Temperature 0 is an explicitly configured value, not a guarantee of identical outputs. The SDK and service may have their own default networking or retry behavior not configured in this source; no claim is made about those defaults.

## Exercise Generation and Validation

For a generator that implements the three staged methods, the application calls Mermaid generation, block/rubric generation and explanation generation sequentially. It normalizes and validates the result after each stage, then assembles and normalizes the exercise. The final block's full reference-code answer is constructed locally, not requested from the model during the staged explanation call. The application preserves the supplied examples and derives the title from the subject or problem statement.

The individual and batch import endpoints invoke the same `generate_complete_exercise` function. The staged function does not automatically call any of the repair methods after an invalid response; it returns an error for the failed stage. Repair methods remain defined in the code. Rubric candidate revision is a separate, explicitly invoked endpoint, with a candidate-attempt limit of three; it should not be confused with automatic generation repair. A generator without all three staged methods falls back to the legacy single-call method.

## JSON Formats and Limits

The schemas in [`schemas/`](schemas/) are extracted from the pinned source and retain their OpenAI `response_format` wrappers. Mermaid, structural blocks and canonical explanations are declared with `strict: true`; student-flow and natural-language schemas are declared with `strict: false`. The legacy exercise call uses `{"type":"json_object"}` rather than a JSON Schema.

`build_explanation_response_format` creates schema properties from the supplied block IDs and sets the line-number maximum using the number of reference-code lines. The included two-block schema is one generated example. The code applies additional local validation and normalization, including Mermaid and block checks; the schema alone is not the complete validation contract. Consequently, constraints stated in prompts and schemas can differ, and this documentation preserves those differences rather than treating them as identical.

## Historical Reproducibility

The exact system instructions in this snapshot are in `prompts/original-pt/`; English translations are provided for readers. User messages shown in both directories are synthetic examples. Actual user messages contain the inputs supplied for each exercise or attempt. The repository does not establish historical model version, request timestamps, SDK version, seeds, production environment overrides or whether any individual output was regenerated. Verify those from retained run records before making claims about a study's actual generation procedure.
