# AgentX selected recipes

8 GPUs, TP8/PP1, MTP5 gold AL3.61, FlashInfer 0.6.18, 3600s profiling windows; no offloading.

| C | Configuration | Throughput (tokens/s/GPU) ↑ | P90 TPOT (ms) ↓ | KV usage | Prefix hit (trace/server) |
| --- | --- | ---: | ---: | ---: | ---: |
| 1 | TP8 + MTP5 | 1333.13 | 13.73 | 76.0% | 96.83% / 95.76% |
| 1 | TP8 + MTP5 + DCP4 | 1164.49 | 20.47 | 23.0% | 96.51% / 95.36% |
| 4 | TP8 + MTP5 | 538.75 | 227.22 | 97.0% | 98.12% / 36.28% |
| 4 | TP8 + MTP5 + DCP4 | 1882.80 | 27.88 | 66.0% | 97.43% / 95.63% |
| 8 | TP8 + MTP5 | 367.00 | 521.96 | 99.0% | 98.32% / 6.03% |
| 8 | TP8 + MTP5 + DCP4 | 2149.40 | 78.98 | 97.0% | 97.54% / 89.97% |

Throughput counts completed input+output tokens, including cached input, divided by the active-request time envelope and 8 GPUs. It is not output-only throughput or a division by the nominal 3600s window. KV usage is peak occupancy. Latency excludes warmup; KV/prefix counters cover warmup/profile/run-end. C counts root traces, not requests; request cap is 32.

## Measurement provenance

| Rows | SGLang revision | Decode graph limit |
| --- | --- | --- |
| C1, both configurations; C4/C8 no DCP | `1bab460c92578909f54861ac2cdf864b8d425a5b` | 4 |
| C4 DCP4 | `75af570964cafd10eb0897ee8b4e5cc58d40acec` | 8 |
| C8 DCP4 | `75af570964cafd10eb0897ee8b4e5cc58d40acec` | 16 |

C4 no-DCP uses the first complete measurement, not a selected best run or an average. Its second run had throughput 543.11 tokens/s/GPU, P90 TPOT 228.20 ms, KV peak 100.0%, and prefix hit 98.12% / 36.63%.

The six rows are existing measurements; no additional performance runs were performed for this update. Gold acceptance control is distinct from the natural-MTP GSM8K evaluation. Code revision and graph policy differ for C4/C8 on/off comparisons; do not interpret their ratios as isolated DCP kernel speedups.

Target physical/logical token capacity: no DCP 523,200/523,200; DCP4 436,992/1,747,968. Per-row full-precision metrics and provenance are in [performance.csv](performance.csv). The original archive remains an unchanged September11 snapshot containing the newer C4/C8 DCP4 runs, not all six rows.
