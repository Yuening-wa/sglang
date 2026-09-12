# Local cleanup validation — 2026-09-12

User-approved cleanup commit: `47188809ef944855d1e539a5a20c9b855622a0b0`.

Source: parent `ac8cceee77d33ab66cf71bda1db29ba673af8258` plus one
change: delete the 45-line `test_random_quantization_diagnostics`
method from `test/manual/test_sm120_sparse_mla_lse.py`. Production code is unchanged.

Tests ran before committing on the identical working tree. Modified test SHA256:
`3cfcb844a14d330baae5d5fd9f82f81e3c04873d604f203b82e44efcc29f6592`.

## Results

- Four GPU tests passed in 28.42s on SM120 with FlashInfer 0.6.18:
  - analytic base-2 LSE and DCP2/4 partition merge, including empty owners;
  - CUDA graph replay with changed lengths;
  - packed-index page boundaries and mixed batches;
  - packed-index long prefixes.
- 15 warnings: unknown pytest `asyncio_mode` option and existing Torch JIT
  deprecation warnings. No test failures.
- Python syntax, `git diff --check`, Ruff check and format check passed.
  Local Ruff version is 0.15.12; repository pre-commit pins 0.15.1, so this
  does not claim an exact-version full pre-commit or hosted CI run.
- No model service was launched. GPU memory/utilization returned to zero;
  no compute processes remained after testing.

From the matching checkout/environment on an SM120 GPU:

```bash
CUDA_VISIBLE_DEVICES=0 python -m pytest -q -p no:cacheprovider \
  test/manual/test_sm120_sparse_mla_lse.py \
  test/registered/kernel/attention/test_packed_dsa_dcp_indices.py
```

The prior five-GPU-test result belongs to the unmodified published commit;
it has not been relabeled as this four-test cleanup result. Full GSM8K and
3600s performance were not repeated because inference code did not change.

Reviewer approved removal without new blocking findings. Non-blocking follow-ups:
register the SM120 test when a suitable CI runner is confirmed, and strengthen
the analytic merge fixture with non-constant values/production merge coverage.
