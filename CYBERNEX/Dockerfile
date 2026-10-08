# Dockerfile for Edge Device Hardware Simulation
# Restricts container to CPU-only execution with strictly bounded core and memory ceilings.

FROM python:3.11-slim

# Prevent interactive prompts
ENV DEBIAN_FRONTEND=noninteractive

# 1. Enforce strict CPU-only & offline execution environment
ENV CUDA_VISIBLE_DEVICES="-1"
ENV HIP_VISIBLE_DEVICES="-1"
ENV ORT_ENABLE_GPU="0"
ENV ONNXRUNTIME_PROVIDER="CPUExecutionProvider"
ENV HF_HUB_OFFLINE="1"
ENV TRANSFORMERS_OFFLINE="1"
ENV DISABLE_TELEMETRY="1"
ENV DO_NOT_TRACK="1"

# 2. Install minimal system packages for audio and psutil
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    libasound2-dev \
    espeak \
    procps \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# 3. Copy dependencies and install
COPY requirements.txt /app/
RUN pip install --no-cache-dir -r requirements.txt \
    vosk \
    psutil \
    pyttsx3

# 4. Copy project source tree
COPY . /app/

# 5. Expose web HUD port
EXPOSE 5000

# 6. Default execution: Edge simulation with restricted CPU threads
CMD ["python", "run.py", "--dashboard", "--cores", "2", "--ram-limit-mb", "1024"]
