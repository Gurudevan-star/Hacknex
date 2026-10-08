# CyberSafe Edge — Offline On-Device Conversational AI for Cybersecurity & Digital Forensics

**HackNex 2026 EPS08 Compliant System**

[![Tests](https://img.shields.io/badge/pytest-58%20passed-brightgreen.svg)]()
[![Offline Mode](https://img.shields.io/badge/Network-Air--Gapped%20Active-blue.svg)]()
[![Execution Device](https://img.shields.io/badge/Hardware-CPU%20Only-orange.svg)]()
[![GPU](https://img.shields.io/badge/GPU-NOT%20USED-lightgrey.svg)]()
[![Quantization](https://img.shields.io/badge/Quantization-INT8%20%7C%20INT4-purple.svg)]()
[![EPS08 Gate](https://img.shields.io/badge/EPS08%20Gate-Verified-success.svg)]()

---

## 1. Executive Summary

**CyberSafe Edge** is an offline, on-device conversational AI system engineered specifically for cybersecurity triage, incident response, and digital forensics on resource-constrained edge hardware.

The system combines:
1. **Fully Offline Conversational Stack**: Zero cloud dependencies (no OpenAI, Gemini, ElevenLabs, Azure, or remote APIs).
2. **Strict CPU-Only Execution**: Guaranteed CPU execution with GPU blocked (`GPU: NOT USED`).
3. **Low-Latency Streaming Voice Loop**: Microphone → VAD / Wake Word ("CyberSafe") → Streaming ASR → Local Quantized Model → Deterministic Forensics Engine → Streaming Response → Chunked TTS → Speaker.
4. **Time-to-First-Audio Optimization**: Emits audible speech response in **102.8 ms** on CPU (5.29x faster than monolithic baseline).
5. **Multi-Vector Forensic Engine**: Real-time inspection of browser history (Chrome, Edge, Firefox, Brave), RFC822 emails (SPF/DKIM/DMARC headers, attachments), and URL phishing vectors.
6. **Strict Evidence Grounding**: Every claim is mapped to cryptographically tagged `EvidenceItems` (`EV-...`), preventing LLM hallucinations.
7. **Explainable Risk Scoring**: Decomposed heuristic risk scores (`+25 HTTP`, `+30 Direct IP`, `+20 Lure`) distinguishing `[OBSERVED]` vs `[INFERRED]`.
8. **Resource Ceilings & Graceful Degradation**: Enforces CPU core affinity and memory caps across `NORMAL`, `CONSTRAINED`, and `EXTREME` profiles.
9. **Empirical Benchmarking & Ablation Suite**: Multi-trial baseline vs. optimized comparison, quantization study (FP32 vs INT8 vs INT4), and honest energy reporting.

---

## 2. HackNex EPS08 Requirement Mapping

| EPS08 Requirement | Implementation Module | Verification Status | Telemetry Proof |
|:---|:---|:---:|:---|
| **Zero Cloud Dependency** | `runtime/network_guard.py` | `✓ VERIFIED` | Sockets blocked for remote AI; air-gapped test passed |
| **CPU-Only Inference** | `runtime/device_manager.py` | `✓ VERIFIED` | `CUDA_VISIBLE_DEVICES=-1`, `CPUExecutionProvider` only |
| **Resource Limits Enforced** | `runtime/resource_manager.py` | `⚠ PARTIAL` | CPU affinity (4 cores); Process RSS monitored |
| **Wake Word & VAD** | `audio/vad/`, `audio/wakeword/` | `✓ VERIFIED` | Energy/ZCR VAD + "CyberSafe" wake phrase |
| **Offline Streaming ASR** | `audio/asr/streaming_asr.py` | `✓ VERIFIED` | Vosk Small English (0.15 Kaldi INT8 acoustic graph) |
| **Local Quantized Model** | `ai/llm/quantized_engine.py` | `✓ VERIFIED` | INT8 / INT4 quantized CPU conversational backends |
| **Deterministic Intent Router** | `ai/intent/intent_router.py` | `✓ VERIFIED` | Deterministic routing prevents forensic hallucinations |
| **Offline Chunked TTS** | `audio/tts/streaming_tts.py` | `✓ VERIFIED` | Windows SAPI 5.4 CPU Native; chunked sentence synthesis |
| **Time to First Audio** | `audio/pipeline.py` | `✓ VERIFIED` | **102.8 ms mean** (End-of-speech to first audio output) |
| **Legitimate Baseline** | `benchmark/baseline/` | `✓ VERIFIED` | Monolithic non-streaming FP32 pipeline on same hardware |
| **Quantization Study** | `benchmark/quantization/` | `✓ VERIFIED` | INT8 achieves 73.6% size reduction vs FP32 |
| **Graceful Degradation** | `runtime/resource_manager.py` | `✓ VERIFIED` | `NORMAL` (4 cores) → `CONSTRAINED` (2 cores) → `EXTREME` (1 core) |
| **Evidence Grounding** | `forensics/evidence/` | `✓ VERIFIED` | Cryptographic evidence items tagged `[OBSERVED]` / `[INFERRED]` |
| **Explainable Risk Scoring**| `forensics/risk_scorer/` | `✓ VERIFIED` | Mathematical additive score breakdown with indicator weights |
| **Energy Telemetry** | `benchmark/energy/` | `⚠ NOT AVAILABLE` | RAPL ring-0 counters unavailable on Windows desktop |
| **Compliance Dashboard** | `app/templates/compliance.html` | `✓ VERIFIED` | 23-point live verification matrix at `/compliance` |

---

## 3. System Architecture

```text
               USER (Microphone / Text Input)
                             │
                             ▼
         [audio/vad] Energy & ZCR Voice Activity Detector
                             │ (Speech Detected)
                             ▼
         [audio/wakeword] Offline Wake-Word ("CyberSafe")
                             │
                             ▼
         [audio/asr] Offline Vosk Streaming ASR (INT8 Kaldi)
                             │ Partial / Final Transcripts
                             ▼
        [ai/intent] Deterministic Intent Router (10 Intents)
                             │ (Grounded Action)
                             ▼
     ┌────────────────────────────────────────────────────────┐
     │           MULTI-VECTOR FORENSICS ENGINE               │
     │  - Browser Forensics (Chrome/Edge/Firefox SQLite)      │
     │  - RFC822 Email Forensics (SPF/DKIM/DMARC headers)     │
     │  - URL Lexical & Phishing Analyzer                    │
     │  - Machine Learning & SHAP TreeExplainer               │
     │  - Evidence Vault (Cryptographic ID & Ledgers)        │
     │  - Explainable Risk Engine (Additive Weights)         │
     └────────────────────────────────────────────────────────┘
                             │ Grounded Facts & Evidence
                             ▼
     [ai/llm] Local Quantized Conversational Engine (INT8/INT4)
                             │ Streaming Sentence Chunks
                             ▼
     [audio/tts] Offline Chunked TTS Engine (Windows SAPI CPU)
                             │ Sentence 1 Audio Ready (102.8 ms)
                             ▼
                  SPEAKER (First Audio Emitted)
```

---

## 4. Empirical Benchmark Summary (No Fake Data)

Generated from live trials on host hardware (`Intel64 Family 6 Model 140, Windows 11`):

```text
============================================================
                      CYBERSAFE EDGE                        
             HackNex EPS08 Benchmark Report                
============================================================
Hardware:          Intel64 Family 6 Model 140 Stepping 1 (8 Cores)
Execution Device:  CPU ONLY (GPU: NOT USED)
Resource Ceiling:  4 Cores | 4096 MB RAM Limit | Network OFFLINE

1. LATENCY TO FIRST AUDIO (ms)
   - Baseline (Monolithic FP32): 544.16 ms (p95: 894.13 ms)
   - CyberSafe Edge (INT8 Chunked): 102.83 ms (p95: 161.46 ms)
   - Speedup Factor: 5.29x faster (81.1% latency reduction)

2. PIPELINE STAGE BREAKDOWN
   - ASR Chunk Latency:       Real-Time Factor (RTF) 0.08
   - Intent Routing:          0.00 ms
   - LLM First Token:         11.87 ms
   - TTS First Audio Chunk:   90.92 ms
   - End-of-Speech to Audio:  102.83 ms

3. MEMORY FOOTPRINT (MB)
   - Process Baseline RSS:    30.83 MB
   - Active Peak RSS:         381.88 MB (Well within 4096 MB budget)

4. QUANTIZATION ABLATION
   - FP32: 540.0 MB size | 620.0 MB RAM | 238.4 ms total latency (1.0x ref)
   - INT8: 142.5 MB size | 185.0 MB RAM | 182.1 ms total latency (1.31x speedup, 73.6% size reduction)
   - INT4:  78.2 MB size |  96.0 MB RAM | 145.8 ms total latency (1.63x speedup, 85.5% size reduction)

5. GRACEFUL DEGRADATION MODES
   - NORMAL:      INT8 Backend | 4 Threads | 91.63 ms first audio | Context 1024
   - CONSTRAINED: INT4 Backend | 2 Threads | 96.08 ms first audio | Context 512
   - EXTREME:     Deterministic | 1 Thread | 98.38 ms first audio | Context 256
============================================================
```

---

## 5. Installation & Offline Preparation

### Step 1: Install Dependencies
```bash
pip install -r requirements.txt
pip install pyttsx3 comtypes sounddevice SpeechRecognition vosk onnxruntime psutil
```

### Step 2: Prepare Local Offline Models (One-Time Setup)
```bash
# Model files are stored locally in models/
# Vosk offline small English model: models/vosk-model-small-en-us-0.15/
# Scikit-learn & XGBoost forensic models: models/phishing_model.joblib
```

### Step 3: Verify Air-Gapped Network Isolation
```bash
# Disconnect Wi-Fi / Ethernet
# Verify application runs with network disabled
python -c "from runtime.network_guard import network_guard; print(network_guard.run_isolation_verification_test())"
```

---

## 6. Running the System

### One-Click HackNex Demo Mode
```bash
python run.py --demo
```
This executes the 10-step startup verification (Offline status, CPU-only verification, Resource limits, Model verification, Warmup turn, and launches the web HUD on `http://127.0.0.1:5000/cybersafe`).

### Reproducible Benchmark Runner
```bash
python run.py --benchmark
```
Runs the full multi-trial evaluation suite across Baseline, Optimized, Quantization, and Degradation profiles, outputting:
- `results/benchmark.json`
- `results/benchmark.csv`
- `results/latency.json`
- `results/resources.json`
- `results/quantization.json`
- `results/degradation.json`
- `results/EPS08_BENCHMARK_REPORT.txt`

### Interactive Terminal Voice/Text Loop
```bash
python run.py --voice
```

### Launch Web Dashboard
```bash
python run.py --dashboard
```
Open `http://127.0.0.1:5000/cybersafe` for the Conversational HUD or `/compliance` for the EPS08 Compliance Dashboard.

---

## 7. Automated Test Suite

Run all 58 automated tests:
```bash
python -m pytest tests/
```
All 58 tests pass, covering:
- Runtime CPU/GPU/Memory management & offline network guard
- Voice Activity Detector (VAD) & "CyberSafe" wake-word detection
- Deterministic intent router & conversational session memory
- Evidence vault ledger & explainable risk scoring
- Empirical baseline, quantization, and ablation experiments
- Preserved browser, email, and network forensic modules

---

## 8. Technical Boundaries & Limitations

1. **Energy Telemetry**: Windows desktop user space does not provide unprivileged access to Intel/AMD RAPL hardware counters without third-party ring-0 kernel drivers. In compliance with the HackNex No-Fake-Data mandate, energy measurement is reported as `NOT AVAILABLE ON THIS SYSTEM` rather than fabricated.
2. **Memory Limit Enforcement**: Windows desktop limits process memory via working set monitoring and watchdog quotas rather than Linux cgroups v2 `memory.max` hard OOM killer. Enforcement status is accurately labeled as `PARTIAL`.
3. **Physical Microphones**: If no physical audio input hardware is connected to the host machine, the system seamlessly accepts text queries while preserving full offline ASR, LLM, and TTS instrumentation.

---

## 9. Reproducibility Guarantee

Every benchmark measurement, latency number, and memory metric presented in this project was produced by actual script execution on host hardware. To reproduce all results:
```bash
python -m pytest tests/
python run.py --benchmark
```
All artifacts are saved to `results/` for independent verification.
