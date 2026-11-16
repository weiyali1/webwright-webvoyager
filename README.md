# Webwright WebVoyager Results

> **Documentation draft for the 2026-11-16 batch.** Numerical values and task-specific details below are retained from the earlier submission as a template. They will be updated after this batch's evaluation is complete.

**Webwright (Qwen3.8-Max): 600/615 tasks, 97.56%.**

This repository publishes a self-reported system-level evaluation on 615 tasks adapted from the [browser-control-fable-5](https://github.com/omxyz/webvoyager/tree/main/benchmark/data/browser-control-fable-5) task collection in omxyz/webvoyager.

Reported by [XYDT](https://www.xydigit.com/).

The exact task definitions used for execution and evaluation are published in [`benchmark/webvoyager/`](benchmark/webvoyager/). The agent and external judge used the same updated task instructions. The reported score applies to this date-updated collection of 615 tasks.

The external judge is GLM-5.3-Flash, using the official WebVoyager judging prompt.

Webwright uses an agent-generated self-verification and repair loop during task execution. The agent writes the internal critical points and expected answer; feedback can trigger script revisions and additional runs within the same task record. Multiple `final_runs/run_NNN` directories preserve these internal iterations, while each unique task contributes one final evaluation result.

| Outcome | Tasks |
| --- | ---: |
| Success | 600 |
| Failure | 11 |
| Missing evidence | 4 |
| Total | 615 |

- [Batch documentation and framework workflow](results/webvoyager-2026-11-16/README.md)
- [Evaluation methodology](results/webvoyager-2026-11-16/METHODOLOGY.md)
- [Scoring-selection template](results/webvoyager-2026-11-16/SCORING_CORRECTION.md)
- [Exact task collection](benchmark/webvoyager/)
- [Evaluation scripts and publication status](evaluator/)
- [External evaluation outputs and publication status](evaluation/)
- [Execution evidence downloads](https://github.com/weiyali1/webwright-webvoyager/releases/tag/webvoyager-2026-11-16)
- [Archive manifest](https://github.com/weiyali1/webwright-webvoyager/releases/download/webvoyager-2026-11-16/PACKAGING_MANIFEST.json)
- [Archive checksums](https://github.com/weiyali1/webwright-webvoyager/releases/download/webvoyager-2026-11-16/SHA256SUMS.txt)
- [Frozen task snapshot](https://github.com/weiyali1/webwright-webvoyager/tree/6bfe6d6e68c35562a5bd76f65c033a7d8075d486/benchmark/webvoyager)

The 15 ZIP archives contain task records grouped by website.

## Scoring correction template

The score is corrected to 600/615 (97.56%) after reinterpreting the saved judge outputs. The original evaluation script and raw records are unchanged. See [the correction report](results/webvoyager-2026-11-16/SCORING_CORRECTION.md).
