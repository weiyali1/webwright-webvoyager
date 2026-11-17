# Judge Errors and First-Valid-Verdict Scoring

This report applies to the completed `webvoyager-2026-11-16` batch. It replaces carried-over template descriptions with this batch's actual records. The final result is **599 SUCCESS, 14 NOT SUCCESS, and two missing-evidence outcomes out of 615**, with **zero unresolved tasks** and a success rate of **97.39%**.

The authoritative aggregate is [`evaluation/summary.json`](evaluation/summary.json). Its `task_results` entries identify each selected record, raw status, response ID, generation cap, fingerprint, and selection method. The original evaluator and saved per-round records are unchanged by this report.

## First-Round Saved Statuses

The 15 website-level `all_results.json` files together contain exactly 615 unique task IDs:

| Saved first-round status | Tasks |
| --- | ---: |
| `success` | 591 |
| `failure` | 12 |
| `parse_error` | 10 |
| `missing_evidence` | 2 |
| **Total** | **615** |

These are the saved states after the website processes and their resumptions. First-round raw parse errors remain visible in the repository; aggregation preserves those records.

## API Errors Are Not Verdicts

The evaluator treats retryable transport failures, including read timeouts, as `api_error`. The launch settings allowed three additional API attempts, with ordinary transport backoff of 2, 4, and 8 seconds. After exhaustion it saved the error, stopped that process, and left unfinished tasks pending. Website evaluations were resumed in their original directories with the same configuration and selection manifest.

`--resume` skips saved `success`, `failure`, `missing_evidence`, and `parse_error` statuses. It can retry API/evaluation errors and pending tasks. An infrastructure failure is never relabeled NOT SUCCESS solely to complete the score. Error/attempt histories are retained where the harness saved them; they are not represented as an exhaustive append-only log of every launch.

The final published `Wolfram Alpha--43` retry records four API attempts: three transport timeouts followed by a real GLM SUCCESS response with `finish_reason: stop`. Its configured cap is 131072, while reported completion usage is 27986 tokens. Neither the timeouts nor the cap alone establish why earlier requests did not return a verdict.

## Parse Errors and Literal Verdict Recovery

The original parser checks the final non-empty response line. It can miss an explicit verdict when explanatory text follows it. It also rejects `finish_reason: length` as truncated. The provider's `reasoning_content` is not treated as a final answer; recovery examines the saved textual `judge_reasoning` only.

For offline recovery, output must be complete (`finish_reason: stop`). After stripping presentation Markdown, a line must explicitly state SUCCESS or NOT SUCCESS, optionally with a verdict-style label and a trailing explanation. Matching is case-sensitive, and contradictory explicit labels make the output ambiguous. The audit does not infer a verdict from generally favorable prose or use an arbitrary substring occurrence of SUCCESS.

Five initial `parse_error` records already contain such an explicit final verdict. They supply the first valid result even though their raw status remains `parse_error`:

| Task | Initial explicit GLM verdict | Selection |
| --- | --- | --- |
| `BBC News--28` | SUCCESS | Retain first-round response |
| `Cambridge Dictionary--12` | NOT SUCCESS | Retain first-round response |
| `Cambridge Dictionary--27` | SUCCESS | Retain first-round response |
| `Cambridge Dictionary--40` | SUCCESS | Retain first-round response |
| `Google Search--0` | SUCCESS | Retain first-round response |

Their later duplicate judgments remain published but do not replace those earlier outcomes. In particular, `Cambridge Dictionary--12` is a failure from its earliest explicit GLM verdict. The `selection_method` field records these literal verdict extractions as `offline_explicit_verdict_line`.

## All Ten Initial Parse-Error Tasks

| Task | Initial issue | First valid selected outcome | Selected cap |
| --- | --- | --- | ---: |
| `ArXiv--7` | Token-limited output in website summary; separate saved API-error state also exists | NOT SUCCESS, first supplement | 65536 |
| `ArXiv--15` | Token-limited output | SUCCESS, second supplement | 131072 |
| `BBC News--28` | Explicit verdict followed by explanation | SUCCESS, initial response | 32768 |
| `Cambridge Dictionary--12` | Explicit verdict followed by explanation | NOT SUCCESS, initial response | 32768 |
| `Cambridge Dictionary--15` | No explicit SUCCESS / NOT SUCCESS verdict line | SUCCESS, first supplement | 65536 |
| `Cambridge Dictionary--27` | Explicit verdict followed by explanation | SUCCESS, initial response | 32768 |
| `Cambridge Dictionary--40` | Explicit verdict followed by explanation | SUCCESS, initial response | 32768 |
| `Google Flights--13` | No explicit SUCCESS / NOT SUCCESS verdict line | SUCCESS, first supplement | 65536 |
| `Google Search--0` | Explicit verdict followed by explanation | SUCCESS, initial response | 32768 |
| `Wolfram Alpha--43` | Token-limited output | SUCCESS, final published retry | 131072 |

The remaining five tasks without an earlier explicit verdict therefore contribute four successes and one failure from later GLM responses. The selected input fingerprints, task definitions, answer hashes, and run selections agree across their published rounds.

## Supplement Rounds and Generation Budgets

The initial 10 harness-marked parse errors were submitted again at 65536 tokens. At that point, the five explicit verdicts above had not been recovered by the offline audit, so those five also received duplicate judge calls. The final selection excludes those duplicates rather than selecting whichever response is more favorable.

| Published supplement directory | Tasks | Actual cap | Saved raw outcomes |
| --- | ---: | ---: | --- |
| [`parse_error_retry_65536_20261116_215118`](evaluation/parse_error_retry_65536_20261116_215118/) | 10 | 65536 | 6 success, 2 failure, 1 parse error, 1 API error |
| [`parse_error_retry_65536_20261117_000041`](evaluation/parse_error_retry_65536_20261117_000041/) | 2 | 131072 | `ArXiv--15` success; `Wolfram Alpha--43` API error |
| [`wolfram_alpha_43_retry_131072_20261117_075320_0001`](evaluation/wolfram_alpha_43_retry_131072_20261117_075320_0001/) | 1 | 131072 | `Wolfram Alpha--43` success |

The original website rounds used `max_tokens: 32768`. The second supplement contains 131072 in its `evaluation_config.json` despite `65536` in its directory name. Initial timeouts were 300 seconds; later documented retries increased the timeout to 900 seconds. Timeout settings are supplied by the launch command rather than stored in the round configuration JSON.

The judge model, prompt hashes, temperature, seed policy, source screenshot cap, image transport, and request options are identical across the 18 configurations. The output caps are 32768 for the first round, 65536 for the first supplement, and 131072 for the later supplements. The aggregate uses the earliest valid SUCCESS or NOT SUCCESS for each task; later duplicate verdicts remain in the published records.

Supplement rounds judge the same saved task instructions, selected answers, and screenshots. The aggregate combines the saved GLM responses using the first-valid-verdict rule described below.

## Final Selection and Denominator

For each task ID, valid candidates are ordered by the saved `evaluated_at` timestamp and source path. That timestamp records evaluation start, not response completion. The earliest valid result is retained; later duplicates are excluded from scoring and remain available for inspection. No conflicting valid labels were found in the published candidate set.

| Source of selected outcome | SUCCESS | NOT SUCCESS | Missing evidence | Tasks |
| --- | ---: | ---: | ---: | ---: |
| First-round structured verdicts | 591 | 12 | 0 | 603 |
| First-round explicit verdict text recovery | 4 | 1 | 0 | 5 |
| First valid 65536-token supplement verdicts | 2 | 1 | 0 | 3 |
| First valid 131072-token supplement verdicts | 2 | 0 | 0 | 2 |
| Input validation | 0 | 0 | 2 | 2 |
| **Final total** | **599** | **14** | **2** | **615** |

The two missing-evidence tasks are `Google Flights--12` and `Google Search--11`, both missing `answer.txt` in the selected run. They are not GLM failure responses, but both count as unsuccessful and remain in the 615-task denominator.

The 14 first-valid NOT SUCCESS outcomes are:

- ArXiv: `ArXiv--7`, `ArXiv--20`, `ArXiv--33`, `ArXiv--34`.
- BBC News: `BBC News--24`.
- Cambridge Dictionary: `Cambridge Dictionary--12`.
- GitHub: `GitHub--6`, `GitHub--24`.
- Google Flights: `Google Flights--36`, `Google Flights--38`.
- Google Map: `Google Map--33`.
- Google Search: `Google Search--2`, `Google Search--34`.
- Wolfram Alpha: `Wolfram Alpha--33`.

Success rate = 599 / 615 × 100%, displayed as **97.39%** by truncating to two decimal places. Merging only the original evaluator's structured accepted statuses also gives 599 success, 14 failure, and two missing evidence; the literal-text recovery changes the selected provenance of five tasks, not these totals.

## Preserved Historical Record Discrepancy

For `ArXiv--7`, [`ArXiv/all_results.json`](evaluation/ArXiv/all_results.json) saves a token-limit `parse_error`, while its [individual judge record](evaluation/ArXiv/judge_results/ArXiv--7_b7c3b55a.json) saves an earlier timeout `api_error`. Both are unresolved historical states and neither supplies a valid verdict. The first valid result is NOT SUCCESS in the 65536-token supplement.

Both raw files retain their saved values. The discrepancy is also listed in `evaluation/summary.json` under `data_quality_notes`. The per-round files describe their saved states; the aggregate lists the final outcome selected for each task ID.

## Publication Integrity

The score is supported by the saved GLM responses and the evaluator's two input-validation outcomes. The publication copied 826 existing evaluation output files byte-for-byte and added `evaluation/summary.json`; the 18 raw rounds remain intact. This documentation update changes only `README.md`, `METHODOLOGY.md`, and `SCORING_CORRECTION.md`. It changes no task, task lock, screenshot, selected answer, evaluator script, or evaluation record.
