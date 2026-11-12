# Webwright WebVoyager — Pre-execution Task Definitions

Maintained by [XYDT](https://www.xydigit.com/).

This repository publishes the exact task definitions intended for a subsequent Webwright execution and evaluation. It contains **615 unique tasks across 15 websites**. This task-definition publication includes no execution records or benchmark score.

## Task collection and provenance

The collection is adapted from [browser-control-fable-5 in omxyz/webvoyager](https://github.com/omxyz/webvoyager/blob/df42681582be8125c5296473ad8efcd86f926cb6/benchmark/data/browser-control-fable-5/WebVoyager_data.jsonl).

- Fixed upstream comparison commit: `df42681582be8125c5296473ad8efcd86f926cb6`.
- Exact task files intended for execution and evaluation: [`webvoyager/`](webvoyager/).
- Retained upstream comparison file: [`reference/WebVoyager_data.jsonl`](reference/WebVoyager_data.jsonl).
- Upstream file SHA-256: `3be3e58768816c8a0249ba48c4dec5d25a91047a2f9bb0e028bcbba73669ac40`.
- Task IDs, website names, starting URLs, and all fields other than the instruction are unchanged from the fixed upstream reference.
- **565 instructions are unchanged; 50 instructions contain month-name substitutions only**: 20 Booking tasks and 30 Google Flights tasks, with 81 substitutions in total. Day numbers, years, and all non-month instruction text are preserved.

The complete task-by-task changes are documented in [`TASK_UPDATES.md`](TASK_UPDATES.md). File hashes and task IDs are recorded in [`TASK_MANIFEST.json`](TASK_MANIFEST.json), with executable checksum entries in [`SHA256SUMS.txt`](SHA256SUMS.txt).

## Date preparation

Date preparation used **November 14, 2026** as its cutoff. In the Booking and Google Flights files, tasks with an explicitly year-dated trip start before that cutoff were moved forward by one calendar month relative to the local preparation input. All dates within an affected trip were moved together.

Dates without an explicit year were left unchanged. Historical publication dates, historical sports dates, and other dates used as content-search conditions were not treated as expired travel dates.

The earliest explicitly year-dated travel start in this collection is **November 15, 2026**. See the task-update report for the existing weekend-wording inconsistency and the calendar-interval effects of month substitutions. No dates will be silently adapted during an execution using this frozen version.

## Publication before execution

1. Publish the task definitions and their change report before starting the associated execution batch.
2. Record the full Git commit SHA after confirming that it is publicly accessible. The initial task snapshot is also identified by the `tasks-frozen-benchmark-2026-11-12` tag.
3. Use the task files from that exact commit for both execution and external evaluation. Retain the task instruction and frozen commit SHA in the execution provenance.
4. Verify the execution input files against `SHA256SUMS.txt` before starting. The checksum paths are relative to a project directory containing `webvoyager/`.
5. Keep this task snapshot and tag unchanged. If any task definitions need revision before execution, publish and identify a new snapshot first; do not change definitions during the batch or after observing its outcomes.
6. When results are published, link to the exact task commit, retain execution evidence and raw judge outputs, and document the agent, judge, budgets, retry/stopping policy, and score aggregation.

For the benchmark snapshot in a checkout of this repository:

```bash
cd benchmark
git rev-parse HEAD
sha256sum -c SHA256SUMS.txt
```

A future result must identify which task-definition commit it actually used. Publication of these task definitions does not by itself establish any execution result or independent verification.
