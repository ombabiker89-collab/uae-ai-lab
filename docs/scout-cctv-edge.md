# Scout: CCTV edge computing (EyeGo.ai JD analogue)

Tracked in issue #4. Not affiliated with EyeGo.ai.

## What EyeGo's public surface implies

EyeGo.ai positions an AIoT / Physical AI stack:

| Product language | Function |
|---|---|
| Mobser | Computer vision on existing CCTV (RTSP/ONVIF), hardware-agnostic |
| Naseh | Agentic / generative ops layer on events |
| Moean | Continuous compliance / hygiene / SOP checks |
| BRAIN Edge OS | On-prem edge node, zero-latency, sovereignty |

Published JDs (AI / Senior CV Engineer) emphasize Python, C++, ML frameworks, pattern recognition, robotics adjacency, AIoT, and real-time processing.

Industry-adjacent JDs for the same class of product almost always add NVIDIA Jetson, DeepStream/GStreamer, TensorRT, YOLO-family detectors, multi-object tracking, and Docker.

## Reference repos (study these, do not claim they are EyeGo)

### 1. Closest product analogue — Securade HUB

- Repo: https://github.com/securade/hub
- Default branch: `main` only (protected)
- Why: generative-AI edge CV that attaches to existing CCTV; Jetson topic; zone / PPE / proximity plugins

Tree that matters:

```
Configure_Camera.py     # camera / RTSP setup
securade.py             # runtime orchestrator
safety_app.py           # safety workflows
dashboard.py            # operator UI
plugins/
  base_plugin.py
  plugin_manager.py
  yolo_detector.py
  edge_detector.py
  agent_detection_plugin.py   # Naseh-like agent hook
  safety_transform_plugin.py
modelzoo/ configs/ models/ scripts/
Open-GroundingDino/     # zero-shot / open-vocab detection (submodule)
pynvr/                  # NVR helper (submodule)
```

Functionality pattern: ingest → plugin detectors → zone rules → dashboard / alerts. Good template for a thin lab sketch.

### 2. Production NVR reference — Frigate

- Repo: https://github.com/blakeblackshear/frigate
- Default branch: `dev` (not `main`); plus many `dependabot/*` maintenance branches
- Stars ~36k. RTSP, local detectors (Coral / OpenVINO / TensorRT-class), MQTT, recordings, now a `frigate/genai` package

Package map (`frigate/`):

```
camera/          # ingest
video/ motion/   # cheap gate before DNN
object_detection/ detectors/
track/ events/ record/
ptz/ comms/      # ONVIF PTZ + MQTT
api/             # REST
genai/ embeddings/
web/             # operator console (sibling of python package)
docker/
```

Branch lesson: keep `main`/`dev` stable; isolate detector and UI work on short-lived branches. Do not treat `dev` as a release.

### 3. NVIDIA official pipelines (EyeGo-class runtime)

| Repo | Use |
|---|---|
| [NVIDIA-AI-IOT/deepstream_python_apps](https://github.com/NVIDIA-AI-IOT/deepstream_python_apps) | Python bindings + multi-stream samples (`master`) |
| [NVIDIA-AI-IOT/deepstream_reference_apps](https://github.com/NVIDIA-AI-IOT/deepstream_reference_apps) | C++ Tesla/Jetson samples |
| [NVIDIA-AI-IOT/yolo_deepstream](https://github.com/NVIDIA-AI-IOT/yolo_deepstream) | YOLO QAT → TensorRT → DeepStream |
| [NVIDIA-AI-IOT/deepstream-occupancy-analytics](https://github.com/NVIDIA-AI-IOT/deepstream-occupancy-analytics) | People in/out — hospitality / mall analogue |

These are the closest match to a BRAIN Edge OS *runtime* (GStreamer graph, batched TensorRT, hardware decode).

### 4. Compact Jetson + VLM labs

- https://github.com/hemkesh2021-dotcom/Sentinel_Surveillance — Jetson Orin Nano, YOLOv8n+TensorRT, ByteTrack, local VLM, Telegram alerts (`master`)
- https://github.com/ibrahimSumbul/ai_nvr — hybrid edge on existing CCTV/NVR, privacy-first, local LLM (`main`)
- https://github.com/Awesome06/Rakshak — edge perception + VLM triage (`main`)
- https://github.com/hamza-aziz-ai/ai-video-analytics-system — multi-RTSP, YOLO, DeepStream, TensorRT (`main`)

## Suggested lab branch layout (this repo)

Follow the playbook:

```
main
scout/cctv-edge          # this note + issue #4
svc/cctv-edge-node       # later: ingest + detector sketch
model/yolo-trt           # later: export / bench notes
```

Do **not** fork Frigate into this lab unless we need its NVR. Prefer:

1. Read NVIDIA DeepStream Python apps for the pipeline.
2. Read Securade `plugins/` for the product shape.
3. Optionally vendor a tiny RTSP → YOLO → MQTT demo under `notebooks/` or `svc/`.

## UAE market note

Buyers: hotels / F&B groups, malls, airports, industrial HSE, government sites that already paid for Hikvision/Dahua/Axis and will not rip-and-replace.

Residency: keep raw frames on the edge node. Ship events, counts, and clipped evidence only. PDPL and sector CCTV rules beat generic open-data instincts.

Related: issue #1 (Falcon-Perception) if we later want a TII-origin vision backbone instead of stock YOLO.
