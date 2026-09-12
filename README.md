# SM120 packed DSA DCP + native MTP: reviewer evidence

This evidence branch is separate from the three-commit feature branch
[`sm120-packed-dsa-dcp-mtp`](https://github.com/Yuening-wa/sglang/tree/sm120-packed-dsa-dcp-mtp).

## Results

GSM8K, GLM-5.2 NVFP4, 8 SM120 GPUs, TP8/PP1, FP8 KV, natural MTP5:

| Configuration | Primary (1319 questions) | Diagnostic repeat |
|---|---:|---:|
| DCP off |1286/1319 (97.50%)|1280/1319 (97.04%)|
| DCP4 |1288/1319 (97.65%)|1286/1319 (97.50%)|

Five training examples, temperature0,seed42,C4,4096-token limit. Truncations
count as incorrect; zero request errors. Primary was declared in advance;
repeats are not pooled. [Paired score](paired-score.json) passes our1pp
non-inferiority margin; it is not a universal model-equivalence claim.

[AgentX C4/C8 performance table](performance.md) and [CSV](performance.csv)
use MTP5 **gold** AL3.61,3600s profiling windows. They are absolute controlled
recipe results, not natural-MTP throughput or matched DCP-on/off speedup.

## Exact evidence identities

| Source | Evidence |
|---|---|
|75af570964cafd10eb0897ee8b4e5cc58d40acec,base55b4f4f19506c5571d7c4b04b1b47e85790b6db3|Full GSM8K and3600s performance|
|3e930fbbe35185caf012570f44740800a26b8d2e,base822e73ccddc0297e9901042d4aab7fcccc11f1a6|BS11/12 natural rejection/cache/three-stage graph and64-question screen|
|ac8cceee77d33ab66cf71bda1db29ba673af8258,base6dc7b3421b48ab962e9567cb3a595fcb7bfe1e00|Publication candidate:57 CPU,5 GPU,8 production final-page cases and8 upstream cache/backend-resolution tests passed|

The publication rebase preserves the SM120 packed path and upstream generic
HIP empty-prefix guard. Retain explicit `--dcp-comm-backend ag_rs`. Old full
results are not relabeled as a full evaluation of the publication candidate.
No full model was restarted for the final publication rebase.

## Reproduction

- [Portable server launch](server-launch.md)
- [Download full sanitized evidence archive](evidence-20260911.tar.gz)
- [Runtime regression comparison](runtime-screen-3e930fbbe.json)
- Source bundles: [full-evaluation source](source-75af57096.bundle),
  [runtime-regression source](source-3e930fbbe.bundle),
  [publication source](source-ac8cceee77.bundle). Each Git bundle requires its
  corresponding upstream base above. Fetch that base before importing it.

The archive contains complete sanitized per-question answers/token IDs,
contracts/prompts, evaluator, scorer, metric definitions, integration summaries,
and file hashes. The archived README/version names describe the September11
snapshot; the publication mapping above supersedes its pre-upload status.
After extraction, install scipy and run:

```bash
python dcp_paired_quality_gate.py accuracy-d1 accuracy-d4
```

Archive SHA256:
`fb012495cec90ff1c508df0b8f39f6171d55539b1fbf956384244154cf75c17e`.
The exported evaluator additionally needs transformers and the same model
tokenizer for fresh generation; natural evaluation must disable acceptance
simulation. GSM8K source is [openai/grade-school-math](https://github.com/openai/grade-school-math);
its MIT license is included. Model outputs remain subject to applicable model terms.

Model/tokenizer revision and the exact AgentX workload/harness are not exported;
exact external performance replay therefore remains incomplete. No private
paths, hostnames, environment dumps, credentials or internal report URLs are
required by this evidence. PP, other models, HiCache/HiSparse and offloading
are outside the validated recipe.
