# Webwright WebVoyager

Maintained by [XYDT](https://www.xydigit.com/).

This repository holds the task definitions and provides separate locations for future execution evidence, evaluation outputs, and evaluation scripts. The benchmark inputs contain **615 unique tasks across 15 websites**, adapted from the fixed browser-control-fable-5 reference documented below.

## Repository layout

| Directory | Contents |
|---|---|
| [`benchmark/`](benchmark/) | Frozen task definitions, upstream reference, full month-change report, task manifest, and checksums. |
| [`results/`](results/) | Future execution-evidence indexes, artifact manifests, checksums, and archive download links. |
| [`evaluation/`](evaluation/) | Future raw external-judge outputs, judgment-selection records, and final summaries. |
| [`evaluator/`](evaluator/) | Evaluation scripts and public configuration for the corresponding batch. |

No execution results or benchmark score have been published in this repository yet. The result-related directories currently contain instructions for their future contents.

## Task definitions

- Exact task files: [`benchmark/webvoyager/`](benchmark/webvoyager/).
- Full before-and-after task instructions: [`benchmark/TASK_UPDATES.md`](benchmark/TASK_UPDATES.md).
- Task IDs and file hashes: [`benchmark/TASK_MANIFEST.json`](benchmark/TASK_MANIFEST.json).
- Executable task checksums: [`benchmark/SHA256SUMS.txt`](benchmark/SHA256SUMS.txt).

Relative to the fixed upstream collection, **565 instructions are unchanged and 50 differ only in month names** (20 Booking and 30 Google Flights tasks; 81 substitutions). All task IDs and non-instruction fields are retained. Yearless dates were not changed.

## Public task freeze before execution

The benchmark-layout task snapshot is identified by the `tasks-frozen-benchmark-2026-11-12` tag. Record its full commit SHA and use the exact associated task definitions for execution and external evaluation.

```bash
cd benchmark
sha256sum -c SHA256SUMS.txt
git rev-parse HEAD
```

The checksum entries are relative to `benchmark/`. They also work from a separate execution-project directory containing the identical `webvoyager/` files when the checksum file is supplied by its full path.

Keep the frozen task commit and its tag unchanged. If task definitions need revision before execution, publish a new identified snapshot first. Do not alter task definitions during a batch or after observing its outcomes.

## Publishing results later

After execution, place the batch documentation and execution-artifact index under `results/`, the external-judge records and final summary under `evaluation/`, and the evaluation script under `evaluator/`. Large execution archives may be published as Release assets, with their download links, packaging manifest, and SHA-256 checksums recorded under `results/`.

Every reported batch must identify its frozen task commit, agent and judge configuration, retry/stopping policy, and scoring/aggregation rules. Preserve task-level evidence and raw judge outputs so that the result can be audited.
