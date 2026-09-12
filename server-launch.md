# Portable launch instructions

Use the matching GLM-5.2 NVFP4 weights/tokenizer and indicated SGLang SHA, FlashInfer0.6.18 on eight SM120 GPUs. Replace the local model path. DCP_SIZE=1 is the reference;4 is the candidate. This is the original full accuracy command with graph4 and seed42; final shape regression uses graph16 and is separate evidence.

```bash
export MODEL_PATH=/path/to/GLM-5.2-NVFP4
export DCP_SIZE=4
export CUDA_VISIBLE_DEVICES=0,1,2,3,4,5,6,7
export SGLANG_ENABLE_JIT_DEEPGEMM=false
unset SGLANG_SIMULATE_ACC_LEN SGLANG_SIMULATE_ACC_METHOD SGLANG_SIMULATE_ACC_TOKEN_MODE
unset SGLANG_ENABLE_PP_SPEC SGLANG_ENABLE_SPEC_V2 CUDA_LAUNCH_BLOCKING
python -m sglang.launch_server \
  --model-path "$MODEL_PATH" --served-model-name nvidia/GLM-5.2-NVFP4 \
  --host 127.0.0.1 --port 32620 \
  --tp-size 8 --pp-size 1 --dcp-size "$DCP_SIZE" --dcp-comm-backend ag_rs \
  --context-length 1048576 --kv-cache-dtype fp8_e4m3 \
  --cuda-graph-max-bs-decode 4 \
  --moe-runner-backend flashinfer_cutlass --quantization modelopt_fp4 \
  --fp4-gemm-backend flashinfer_cutlass --bf16-gemm-backend torch \
  --disable-shared-experts-fusion --disable-custom-all-reduce \
  --tool-call-parser glm47 --reasoning-parser glm45 \
  --chunked-prefill-size 4096 --max-prefill-tokens 8192 \
  --max-running-requests 32 --mem-fraction-static 0.90 \
  --trust-remote-code --enable-metrics --disable-prefill-cuda-graph \
  --speculative-algorithm EAGLE --speculative-num-steps 5 \
  --speculative-eagle-topk 1 --speculative-num-draft-tokens 6 \
  --random-seed 42
```

For the controlled AgentX performance recipe only, omit --random-seed42, use graph8 at C4 or16 at C8 and set the following simulation knobs. Never enable these for natural accuracy. Admission cap stays32.

```bash
export SGLANG_SIMULATE_ACC_LEN=3.61
export SGLANG_SIMULATE_ACC_METHOD=match-expected
export SGLANG_SIMULATE_ACC_TOKEN_MODE=real-draft-token
```

Exact public performance replay additionally requires the AgentX workload/harness and model/tokenizer revision; those are not supplied by this export.
