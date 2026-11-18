# Evaluation Methodology

This document describes the completed `webvoyager-2026-11-16` batch, judged on November 16–17, 2026. The aggregate is **599 successes, 14 failures, and two missing-evidence tasks out of 615**, or **97.39%**. See [`evaluation/summary.json`](evaluation/summary.json) for the machine-readable totals and task-level provenance.

## System, Tasks, and Task Lock

- Execution system: Webwright with `qwen3.8-max`.
- Reporting organization: [XYDT](https://www.xydigit.com/).
- Base collection: [browser-control-fable-5](https://github.com/omxyz/webvoyager/tree/main/benchmark/data/browser-control-fable-5).
- Evaluated collection: 615 unique tasks across 15 websites.
- Existing task manifest: 565 unchanged instructions; 50 month-edited instructions; 81 month-name substitutions.
- Exact definitions: [`benchmark/webvoyager/`](benchmark/webvoyager/).
- Frozen task-definition commit: `6bfe6d6e68c35562a5bd76f65c033a7d8075d486`.
- Task-definition Git tree: `092ac8034909c70a45b50b9ea5aa03fcf51bbe0e`.
- Evaluation type: self-reported, system-level, post-hoc judging.

To address expired dates in the source tasks, month names were updated for 50 tasks before execution, and the resulting task definitions were committed and frozen.

All 615 evaluated instruction strings were checked against the frozen task definitions and match exactly. No task was removed from the denominator. The task definitions in `benchmark/webvoyager/`, `benchmark/TASK_MANIFEST.json`, `benchmark/SHA256SUMS.txt`, and the retained upstream reference are unchanged from the frozen snapshot. This lock applies to those files, not to every file in `benchmark/`; auxiliary documentation in that directory has changed.

## Execution Budgets and Internal Repair

The `config_snapshot/merged_config.yaml` files for all 615 submitted execution records were checked. They consistently record:

| Execution field | Value |
| --- | --- |
| `model.model_name` | `qwen3.8-max` |
| `model.temperature` | `0.0` |
| `model.enable_thinking` | `true` |
| `model.max_output_tokens` | `4000` (default/text) |
| `model.action_max_output_tokens` | `8000` |
| `model.summary_max_output_tokens` | `4000` |
| `model.request_timeout_seconds` | `600` |
| `agent.step_limit` | `400` |
| `agent.max_run_seconds` | `28800` seconds (8 hours) |
| `agent.max_idle_seconds` | `1800` seconds (30 minutes) |
| `agent.max_self_heal_rounds` | `3` |
| `agent.require_self_reflection_success` | `true` |
| `agent.require_task_success_marker` | `true` |

These are configured limits and completion requirements, not measurements of resources consumed by each task. The step limit applies to the agent loop, rather than to all browser actions or model requests. The execution configuration uses a 4,000-token default/text output budget, an 8,000-token action output budget, and a 4,000-token summary output budget. These are distinct from the external GLM judge's output budgets.

**Internal retry policy:** Within each task record, the agent develops and executes a script, checks it using agent-generated critical points and expected answers, and retries script repair/execution until its own self-reflection check passes or configured stopping limits or an unrecoverable error end the task. With the two completion requirements above enabled, normal successful completion is configured to require a passing self-check and a task-success marker. The self-check supplies repair feedback and gates successful completion.

The reported score is a system-level result that includes these self-verified internal retries. It is not a single-attempt, no-retry baseline. Internal pass/fail labels control the execution workflow; the external GLM evaluation supplies the final success/failure label. A task can also terminate on a configured limit or error.

Numbered `final_runs/run_NNN` directories are internal execution/repair iterations. Of the 615 selected final runs, 258 are `run_001` and 357 have a numeric index of at least 2; the maximum selected index is 22. A selected index is not a count of independently scored benchmark attempts, and the maximum observed index is not an inferred configured retry limit.

The highest-numbered run is the final saved output of this workflow, which uses self-check feedback during repair and stopping. Each task ID contributes one final external outcome. Judge-side API/parse-error retries are described separately below.

## Frozen Input Selection

The evaluator uses the highest numeric `final_runs/run_NNN` within each task record. It does not consult an internal success label, choose a run based on the external verdict, or fall back to an earlier run.

The request contains:

1. The task instruction from that record.
2. The selected run's `answer.txt`, read and stripped of surrounding whitespace.
3. Up to the last five screenshots from that run.

**Answer preparation.** The selected run's `answer.txt` is derived from the model's original answer in the same run's `final_response.txt`. Preparation removes only text unrelated to the answer itself: internal self-check or grading statements, completion markers, and internal artifact references, such as `self-reflection … PASS`, and `predicted_label=1`. All substantive answer content, including its wording, explanations, facts, numerical values, and conclusions, is preserved without rewriting, correction, or supplementation. The original `final_response.txt` remains available in the execution artifacts.

This preparation precedes external evaluation. During judging, the evaluator reads the prepared `answer.txt` as the answer input.

Screenshot order follows `run_manifest.json` when present; otherwise it follows natural filename order. The final five in that order are selected. The evaluator never substitutes `final_response.txt`, `final_script_log.txt`, internal judge files, or older runs for a missing answer.

Each round publishes `selection_manifest.json`, including the task-definition hash, answer-file hash where present, screenshot paths and SHA-256 hashes, chosen record/run, and an input fingerprint. Repeated task records in the published rounds have matching task definitions, answer hashes, selected run, and fingerprints. `--resume` requires the same configuration and the same selection manifest.

Two selected runs have no `answer.txt`: `Google Flights--12` and `Google Search--11`. They receive `missing_evidence` without a judge API call, remain in the denominator, and count as unsuccessful.

## External Judge Configuration

The judge uses the standard prompt text from [WebVoyager's `evaluation/auto_eval.py`](https://github.com/MinorJerry/WebVoyager/blob/main/evaluation/auto_eval.py). The exact saved strings in each round's `prompts.json`, and their hashes in `evaluation_config.json`, are authoritative for this batch. The model, image transport, thinking setting, token budget, and local parser are specified below.

| Judge setting | Actual configuration |
| --- | --- |
| Requested and returned model | `glm-5.3-flash` |
| Prompt variant | `standard` |
| Temperature / seed | `0.0` / not set |
| Maximum attached source screenshots | `5` |
| Request format | JPEG, quality `85`, subsampling `0` |
| Resize / crop | Fit within `1280 × 1280` / no crop |
| Image tiling | Disabled (`--image-tile-height 0`) |
| Image detail | `default` (no explicit detail override in the request) |
| Thinking | On (`enable_thinking: true`) |
| Response transport | Non-streaming |
| First-round `max_tokens` | `32768` |
| Supplement `max_tokens` | `65536`, then `131072` |
| Initial timeout | `300` seconds |
| Later documented retry timeout | `900` seconds |
| Additional transient API retries | `3` per invocation |
| Base retry delay / inter-task delay | `2` seconds / `0.5` seconds |

JPEG conversion and resizing apply only to request-time image transport. The source screenshot bytes and hashes are preserved in the execution artifacts. The actual number of attached screenshots depends on the selected run, up to the configured maximum of five.

All 18 published round configurations have the same requested model, temperature, seed policy, screenshot cap, prompt hashes, image transport, and request options. The output token cap differs in the supplement rounds. Timeout/retry/delay options come from launch settings and the evaluator's defaults; they are not fields in `evaluation_config.json`. Per-invocation attempt histories and observed errors are saved where available.

The published evaluator's SHA-256 is `7101526139ea3a829cc3cbb21d58b148be2906e7038553f21953a449f28279e7`. It matches the local evaluator used for these records. This documentation update does not modify that script.

## Actual Round Layout and Concurrency

The first round started 15 website-specific evaluator processes in parallel, each processing its site's tasks sequentially. Their directories sit directly under [`evaluation/`](evaluation/), for example `Allrecipes/`, `ArXiv/`, and `Google_Search/`. Subsequent API-error resumptions reused the corresponding output directory and frozen configuration.

The three additional published rounds are:

| Directory under `evaluation/` | Tasks | Actual `max_tokens` | Saved raw outcomes |
| --- | ---: | ---: | --- |
| `parse_error_retry_65536_20261116_215118` | 10 | 65536 | 6 success, 2 failure, 1 parse error, 1 API error |
| `parse_error_retry_65536_20261117_000041` | 2 | 131072 | 1 success, 1 API error |
| `wolfram_alpha_43_retry_131072_20261117_075320_0001` | 1 | 131072 | 1 success |

Directory names are historical labels, not reliable configuration descriptions: the directory ending in `20261117_000041` contains `max_tokens: 131072` despite `65536` in its name. Always consult its `evaluation_config.json`.

These 15 website rounds and three supplements are the score's source set. The later, separate all-615 experimental rerun is not merged with them. The 628 rows across the 18 `all_results.json` files include repeated task IDs; they are not 628 distinct tasks.

## API Errors and Resume Behavior

Read timeouts and other retryable transport/API failures are infrastructure errors, not task failure verdicts. With `--api-retries 3`, an invocation can make an initial request and three retries. Ordinary transport retries wait 2, 4, and 8 seconds. The evaluator also handles retryable HTTP statuses; rate-limit responses have a longer base wait and can honor `Retry-After`.

After exhausting transient retries, the evaluator saves `api_error`, leaves other unfinished tasks pending, and stops that process. It can then be restarted using `--resume`, the same output directory, and the same frozen evaluation configuration. Existing `success`, `failure`, `missing_evidence`, and `parse_error` statuses are terminal for resume; API/evaluation errors and pending tasks can be retried. Neither an API timeout nor an exhausted retry budget is converted into NOT SUCCESS.

Saved records include per-invocation request attempts and cached infrastructure-error history where recorded by the harness. The preserved first-round `ArXiv--7` summary and individual record contain different historical unresolved states; their values and the final selected record are documented in [SCORING_CORRECTION.md](SCORING_CORRECTION.md).

The successful final `Wolfram Alpha--43` invocation records three transport errors followed by a fourth request returning SUCCESS with `finish_reason: stop`. Its configured cap was 131072; reported completion usage was 27986 tokens.

## Parse Errors and Supplement Judging

The original parser examines the final non-empty response line after presentation normalization. A clear verdict followed by explanatory prose can therefore be saved as `parse_error`. Independently, `finish_reason: length` is rejected as incomplete even if partial text is present. The provider's `reasoning_content` is not treated as a final verdict; `judge_reasoning` stores the returned textual content used by the evaluator, not necessarily the provider's full private reasoning trace.

The 10 records marked `parse_error` in the website-level `all_results.json` files were sent to a new 65536-token supplement directory. Two still lacked accepted verdicts there; a later 131072-token round covered those two. Further infrastructure retries for `Wolfram Alpha--43` ultimately produced a valid result in the separately published final directory. The prompt hashes and selected input fingerprints match across the published rounds.

After the model runs, an offline audit found five first-round parse-error records with complete explicit verdict lines. Their earliest GLM verdicts are used, and the later duplicate judgments are excluded from scoring. For the other five tasks, the first later valid GLM verdict is used. The full ten-task audit appears in [SCORING_CORRECTION.md](SCORING_CORRECTION.md).

The first-round output cap was 32768 tokens; the supplements used 65536 and 131072 tokens. Each round's `evaluation_config.json` records its actual cap. The aggregate retains the earliest valid verdict, including NOT SUCCESS, and records the source configuration for each selected task outcome.

## First Valid Verdict and Scoring

Each task ID contributes one final outcome. Completed structured `success` and `failure` records are accepted; `missing_evidence` is a separate evaluator input-validation outcome. For records marked `parse_error`, offline recovery accepts only an explicit, unambiguous SUCCESS or NOT SUCCESS verdict line in completed output (`finish_reason: stop`). It normalizes Markdown and permits a verdict label or trailing explanation; it does not infer a label from favorable prose, search arbitrary SUCCESS substrings, or grade the answer again.

Candidates are ordered using their saved `evaluated_at` timestamps and source paths; the earliest valid result is selected. `evaluated_at` is the start of an evaluation invocation for that task, not a response completion timestamp. Later valid duplicates cannot replace the first outcome, including a failure. No conflicting valid labels were found among the published candidates.

| Contribution to final selection | SUCCESS | NOT SUCCESS | Missing evidence | Total |
| --- | ---: | ---: | ---: | ---: |
| First-round structured GLM verdicts | 591 | 12 | 0 | 603 |
| Explicit verdicts recovered from first-round text | 4 | 1 | 0 | 5 |
| First valid verdicts from 65536-token supplement | 2 | 1 | 0 | 3 |
| First valid verdicts from 131072-token supplements | 2 | 0 | 0 | 2 |
| Input-validation outcomes | 0 | 0 | 2 | 2 |
| **Total** | **599** | **14** | **2** | **615** |

The aggregate contains 610 task rows associated with a 32768-token source configuration, three with 65536, and two with 131072. The 610 include both missing-answer tasks, for which no judge call was made; the selected actual model verdict counts by cap are therefore 608, 3, and 2.

Success rate = 599 / 615 × 100%, displayed as **97.39%** by truncating to two decimal places. Unresolved API/parse errors are not silently counted as failures or dropped from the denominator; after combining valid records, none of the 615 tasks remains unresolved. Original per-round summaries can still report incomplete status because their historical records are preserved.

Supplement rounds send the frozen task instruction, selected answer, and screenshots to GLM for judging. The aggregation step selects the first valid verdict from the saved GLM responses and combines those verdicts with the two missing-evidence outcomes.

## Reproduction and Published Evidence

The following illustrates the first-round parameters for one website process. Use private endpoint/key environment variables and an extracted execution-artifact root containing the published task records. Other website processes used the same judging settings and their respective website filters/counts.

```bash
python3 evaluator/evaluate_webvoyager.py \
  --input-root "$ARTIFACT_ROOT" \
  --endpoint "$WEBVOYAGER_EVAL_ENDPOINT" \
  --api-key-env WEBVOYAGER_EVAL_API_KEY \
  --model glm-5.3-flash \
  --model-name glm-5.3-flash \
  --purpose pilot \
  --website Allrecipes \
  --expected-tasks 40 \
  --max-attached-imgs 5 \
  --image-format jpeg \
  --jpeg-quality 85 \
  --image-resize fit \
  --image-tile-height 0 \
  --image-detail default \
  --thinking on \
  --temperature 0 \
  --max-tokens 32768 \
  --timeout 300 \
  --api-retries 3 \
  --retry-delay 2 \
  --delay 0.5 \
  --output-dir "$NEW_EVALUATION_DIR"
```

API-error resume uses the original directory and adds `--resume` without changing the frozen configuration. Changing the generation cap requires a new directory. An unresolved-output supplement replaces the website filter with exact `--task-id` filters, sets the matching `--expected-tasks`, and records its changed budget.

The aggregate [`evaluation/summary.json`](evaluation/summary.json) links all 615 selected source records, configuration caps, raw statuses, response IDs, and fingerprints. Each round publishes its configuration, exact prompts, selection manifest, saved judge records, and summaries. The [execution release](https://github.com/weiyali1/webwright-webvoyager/releases/tag/webvoyager-2026-11-16) contains the task artifacts and screenshot evidence referenced by those records.

The 826 evaluation output files were copied byte-for-byte from the saved local evaluation batch after checks for known credentials and local/private identifiers. No additional substitutions were required in that upload. Public execution archives were exported separately with local identifiers, credentials, and internal connection addresses redacted. This documentation commit changes neither the evaluation records nor the benchmark task definitions.
