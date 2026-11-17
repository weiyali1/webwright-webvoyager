# Webwright WebVoyager Results

**Webwright (Qwen3.8-Max): 599/615 tasks, 97.40%.**

This repository publishes a self-reported system-level, post-hoc evaluation of 615 tasks across 15 websites, adapted from [browser-control-fable-5](https://github.com/omxyz/webvoyager/tree/main/benchmark/data/browser-control-fable-5). Reported by [XYDT](https://www.xydigit.com/).

Batch: `webvoyager-2026-11-16`. External judging ran on November 16–17, 2026. The machine-readable result is [`evaluation/summary.json`](evaluation/summary.json); its `task_results` array identifies the first valid outcome and source record for every task.

## Results

| Item | Value |
| --- | ---: |
| Execution model | Qwen3.8-Max |
| External judge | GLM-5.3-Flash |
| Total unique tasks | 615 |
| SUCCESS | 599 |
| NOT SUCCESS (`failure`) | 14 |
| Missing evidence | 2 |
| Unresolved tasks after aggregation | 0 |
| Success rate | **97.40%** |

Success rate = 599 / 615 × 100% = 97.39837398%, rounded to 97.40%. Both missing-evidence tasks remain in the denominator and count as unsuccessful. Thus 613 tasks have a GLM success/failure verdict, and two have an evaluator input-validation outcome.

| Website | Tasks | SUCCESS | NOT SUCCESS | Missing evidence |
| --- | ---: | ---: | ---: | ---: |
| Allrecipes | 40 | 40 | 0 | 0 |
| Amazon | 40 | 40 | 0 | 0 |
| Apple | 40 | 40 | 0 | 0 |
| ArXiv | 43 | 39 | 4 | 0 |
| BBC News | 39 | 38 | 1 | 0 |
| Booking | 40 | 40 | 0 | 0 |
| Cambridge Dictionary | 41 | 40 | 1 | 0 |
| Coursera | 40 | 40 | 0 | 0 |
| ESPN | 44 | 44 | 0 | 0 |
| GitHub | 41 | 39 | 2 | 0 |
| Google Flights | 40 | 37 | 2 | 1 |
| Google Map | 40 | 39 | 1 | 0 |
| Google Search | 42 | 39 | 2 | 1 |
| Huggingface | 39 | 39 | 0 | 0 |
| Wolfram Alpha | 46 | 45 | 1 | 0 |
| **Total** | **615** | **599** | **14** | **2** |

## Locked Task Collection

The executed instructions are published in [`benchmark/webvoyager/`](benchmark/webvoyager/). The collection's existing [manifest](benchmark/TASK_MANIFEST.json) records 565 unchanged instructions and 50 month-edited instructions, with 81 month-name substitutions. This score applies to that date-updated collection, rather than to an unmodified upstream collection.

The [frozen task snapshot](https://github.com/weiyali1/webwright-webvoyager/tree/6bfe6d6e68c35562a5bd76f65c033a7d8075d486/benchmark/webvoyager) is identified by commit `6bfe6d6e68c35562a5bd76f65c033a7d8075d486`; its `benchmark/webvoyager` Git tree is `092ac8034909c70a45b50b9ea5aa03fcf51bbe0e`. All 615 instructions in the published evaluation records were checked against those task definitions and match exactly.

This documentation update does not change any file under `benchmark/`. Task instructions, task IDs, and their existing lock files are retained.

## Execution and Internal Repair

Webwright performs exploration, script development, execution validation, and repair within each task record. Its internal self-reflection checks can guide script revisions and task termination. Those internal checks are separate from the external score.

All 615 execution configuration snapshots record a 400-step agent-loop limit, an eight-hour runtime limit, and a 30-minute idle limit. These are configured limits, not measured consumption. Internal `final_runs/run_NNN` indices describe execution/repair iterations, not independent benchmark tasks. In this batch, 357 selected runs have indices of at least 2; the maximum selected index is 22.

The evaluator selects the newest timestamped task record and then the highest numeric `run_NNN` within it, without consulting its internal pass/fail label or falling back to an earlier successful run. Each task ID contributes one result.

## External Judge and Actual Configuration

Each request supplies the task instruction, only the selected run's `answer.txt`, and up to its last five screenshots. Screenshots follow the run manifest when present, otherwise natural filename order. `final_response.txt`, execution logs, and internal verdict files are not fallback answer sources.

| Setting | Recorded configuration |
| --- | --- |
| Judge model | `glm-5.3-flash` |
| Prompt variant | `standard` |
| Prompt | WebVoyager judging prompt; exact text is saved in each round's `prompts.json` |
| Temperature / seed | `0.0` / not set |
| Source screenshots | Up to 5 from the selected run |
| Request image transport | JPEG quality 85; fit within 1280 × 1280; no cropping or tiling |
| Image detail / thinking | Default / on |
| First-round output cap | 32768 tokens |
| Invalid-output supplement caps | 65536, then 131072 tokens |
| Transport retries | 3 additional attempts per invocation |
| Read timeout | 300 seconds initially; increased to 900 seconds in later documented retries |

The first round used 15 website-specific evaluator processes started in parallel; each process evaluated its own tasks sequentially. It was not a single 615-task evaluator invocation. The aggregate combines those 15 first rounds with the three published supplement directories. Other experimental batches are not merged into this score.

## API Errors, Parse Errors, and First Valid Outcomes

Transport timeouts and retryable API failures did not count as task failures. The evaluator retried within an invocation; after exhausting its retries it saved the error and stopped that process. It was then resumed with the same configuration and frozen input manifest. A `parse_error` is terminal for `--resume`, so invalid-output supplements used new directories rather than overwriting accepted results.

The 15 first-round `all_results.json` files contain 591 successes, 12 failures, 10 parse errors, and two missing-evidence outcomes. Five parse-error records already contain explicit, complete GLM verdict lines: four SUCCESS and one NOT SUCCESS. Those earliest verdicts are extracted offline; their raw status fields remain unchanged. Only the five tasks without an earlier explicit verdict use later successful parsing of a GLM response: four SUCCESS and one NOT SUCCESS.

The final score therefore uses the **first valid verdict for each task**, including failures. Later duplicates are retained for audit and cannot replace an earlier valid outcome. Offline extraction/aggregation made no model calls; the separately published supplement rounds did make additional GLM calls. Increasing an output cap can affect model reasoning and decisions, so the changed budgets are disclosed rather than described as a uniform-budget evaluation.

The missing-evidence tasks are `Google Flights--12` and `Google Search--11`; both selected runs lack `answer.txt`. No earlier run or alternate answer file was substituted.

See [METHODOLOGY.md](METHODOLOGY.md) for the execution and judging procedure, and [SCORING_CORRECTION.md](SCORING_CORRECTION.md) for all ten initial parse errors, supplement selection, and a preserved historical record discrepancy.

## Published Materials

- [Exact task definitions](benchmark/webvoyager/)
- [Evaluation methodology](METHODOLOGY.md)
- [Error handling and first-valid-verdict audit](SCORING_CORRECTION.md)
- [Evaluator script](evaluator/evaluate_webvoyager.py)
- [Aggregate result and 615 task-level source references](evaluation/summary.json)
- [Website rounds and supplement records](evaluation/)
- [Execution evidence downloads](https://github.com/weiyali1/webwright-webvoyager/releases/tag/webvoyager-2026-11-16)
- [Archive manifest](https://github.com/weiyali1/webwright-webvoyager/releases/download/webvoyager-2026-11-16/PACKAGING_MANIFEST.json)
- [Archive checksums](https://github.com/weiyali1/webwright-webvoyager/releases/download/webvoyager-2026-11-16/SHA256SUMS.txt)
- [Frozen task snapshot](https://github.com/weiyali1/webwright-webvoyager/tree/6bfe6d6e68c35562a5bd76f65c033a7d8075d486/benchmark/webvoyager)

The execution release contains 15 website ZIP archives. The evaluation publication preserves 826 saved output files byte-for-byte and adds the aggregate `evaluation/summary.json`. Original per-round errors remain visible even though every unique task now has a final outcome. The evaluation directory contains judging records and references to execution screenshots; the separate release contains the execution artifacts.
