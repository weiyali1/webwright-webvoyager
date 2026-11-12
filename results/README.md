# Execution Evidence

This directory is reserved for future execution batches; it currently contains no execution results.

Use a separate subdirectory for each batch. Include the system and execution policy, frozen task commit SHA, task-to-record mapping, execution-evidence index, and artifact provenance.

Large task archives may be attached to a GitHub Release. Keep their download links, packaging manifest, and SHA-256 checksums in the corresponding batch subdirectory here. Preserve task IDs and task-record structure, including internal iterations, so that evaluation inputs can be traced to their execution evidence.

The frozen inputs are in [`../benchmark/`](../benchmark/). External-judge records and final summaries belong in [`../evaluation/`](../evaluation/).
