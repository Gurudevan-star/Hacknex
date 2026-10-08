# LLM Audit Report: Current Conversational Engine Assessment

**Repository:** `c:/hacknex/1`  
**Target Component:** `ai/llm/quantized_engine.py`  
**Date:** 2026-10-08  
**Auditor:** Senior Edge-AI Systems Engineer  

---

## 1. Executive Summary

An exhaustive technical audit of `ai/llm/quantized_engine.py` and its interacting components (`audio/pipeline.py`, `benchmark/quantization/quant_comparator.py`, `benchmark/baseline/baseline_pipeline.py`, `runtime/resource_manager.py`) was performed.

### Verdict:
- **Is a real neural model currently loaded?** **NO.**
- **Is `llama-cpp-python` used in code execution?** **NO.** Although `llama-cpp-python` (version 0.3.36) is installed in the Python environment, `quantized_engine.py` does not import or call `llama_cpp.Llama`.
- **Is `onnxruntime` used in code execution?** **NO.** Although `onnxruntime` (version 1.30.0) is installed in the Python environment, `quantized_engine.py` does not import or instantiate an `onnxruntime.InferenceSession`.
- **Are GGUF files loaded?** **NO.** There are currently no `.gguf` weights present in `models/` or loaded at runtime.
- **Are INT4, INT8, and FP32 profiles only metadata?** **YES.** The quantization profiles (`CyberSafe-Grounded-INT8`, `CyberSafe-Grounded-INT4`, `Baseline-Standard-FP32`) in `MODELS_METADATA` only provide static metadata numbers (e.g. `memory_footprint_mb: 185.0`, `file_size_mb: 142.5`), while token generation is simulated by splitting a deterministic string with `time.sleep(0.001)`.

---

## 2. Line-by-Line Technical Audit of `ai/llm/quantized_engine.py`

### 2.1 Quantization Metadata Dictionary (Lines 20–61)
```python
MODELS_METADATA = {
    "CyberSafe-Grounded-INT8": {
        "version": "1.4.0",
        "parameters": "135M",
        "file_size_mb": 142.5,
        "quantization": "INT8",
        "precision_bits": 8,
        "context_length": 1024,
        "default_threads": 4,
        "memory_footprint_mb": 185.0,
    },
    ...
```
*Finding:* These profiles describe legitimate quantization targets (135M model, Q8 / Q4 / FP32), but they exist purely as descriptive dictionary values. No weights are allocated or referenced on disk.

### 2.2 Token Generation Logic (Lines 105–128)
```python
# Build grounded narrative text deterministically based on verified evidence
text_response = self._synthesize_grounded_narrative(intent, forensic_context, query)
words = text_response.split(" ")

accumulated = []
for idx, word in enumerate(words):
    if t_first_token is None:
        t_first_token = time.perf_counter()

    accumulated.append(word)
    current_text = " ".join(accumulated)

    # High-speed offline CPU token dispatch pacing
    time.sleep(0.001)

    yield {
        "token": word + (" " if idx < len(words) - 1 else ""),
        "text": current_text,
        "is_first_token": idx == 0,
        "is_final": idx == len(words) - 1,
        "first_token_ms": round((t_first_token - t_start) * 1000, 2),
        "current_latency_ms": round((time.perf_counter() - t_start) * 1000, 2),
        "backend": backend,
    }
```
*Finding:* Token generation does not invoke an autoregressive transformer or quantized kernel. It formats a deterministic string from the heuristic knowledge base/forensic slots, splits the string on whitespace, and yields words with a 1 ms sleep.

### 2.3 Environmental Prerequisites
- `llama-cpp-python`: Installed (`0.3.36`), CPU-capable with AVX2.
- `onnxruntime`: Installed (`1.30.0`), CPUExecutionProvider active.
- `huggingface_hub`: Installed.
- `models/` directory: Contains ML models (`phishing_model.joblib`, `logistic_regression`, `vosk-model-small-en-us-0.15`), but lacks LLM subdirectories `models/fp32/`, `models/int8/`, `models/int4/`.

---

## 3. Required Engineering Plan to Reach Research-Grade Real Inference

To satisfy the HNX26EPS08 challenge and the 10 strict requirements:

1. **Model Abstraction Layer (`ai/llm/backends/`)**:
   - `ModelBackend` base interface with streaming generation, token latency tracking, and context tracking.
   - `GGUFBackend`: Utilizing `llama_cpp.Llama` with CPU threads, `n_ctx`, `n_batch`, and generator token-level streaming.
   - `ONNXBackend`: Utilizing `onnxruntime.InferenceSession` with `CPUExecutionProvider`.
   - `DeterministicBackend`: Retaining the current zero-weight rule-based logic as a zero-resource fallback.

2. **Real Model Assets (`models/`)**:
   - `models/int4/`: Genuine Q4_K_M GGUF model (`SmolLM2-135M-Instruct.Q4_K_M.gguf`, ~100.5 MB).
   - `models/int8/`: Genuine Q8_0 GGUF model (`SmolLM2-135M-Instruct.Q8_0.gguf`, ~138.1 MB).
   - `models/fp32/`: Genuine F32 GGUF model (`SmolLM2-135M-Instruct.F32.gguf`, ~514.8 MB) or compact ONNX CPU model.
   - Automatic model discovery at startup with verification of file existence, size, and quantization type.

3. **Forensic Grounding Preservation**:
   - The user query, forensic evidence items, and knowledge base snippets are structured into a prompt template passed to the actual model backend.
   - Streaming generator yields real generated tokens while tracking first-token latency, tokens/sec, and memory consumption.
   - If memory constraints reach extreme levels or models are absent, `DeterministicBackend` acts as a fail-safe.

4. **Resource Manager & Benchmark Integration**:
   - Dynamic switching across FP32, INT8, INT4, and Deterministic fallback based on memory thresholds.
   - Empirical benchmarks reporting real process RSS and generation speed.
