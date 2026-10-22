# Streaming Log Anomaly Detector (SLAD) 

A streaming pipeline for server logs that aims to flag anomalous time windows with low memory overhead, using probabilistic data structures and online machine learning.

This project is **in active development**. It applies ideas from my JKU coursework (Big Data Sketching, Machine Learning) to a small, production-style service. The status section below says exactly what works today and what is planned.

## Status

- [x] Simulated log stream (`Faker`, `asyncio`)
- [x] FastAPI service exposing a health endpoint
- [ ] Replay of labelled real logs from [LogHub](https://github.com/logpai/loghub) (BGL first)
- [ ] Window features computed with streaming sketches (Apache DataSketches)
- [ ] Online anomaly detector (River) plus simple baselines
- [ ] Evaluation: precision, recall, F1 and detection delay on labelled data
- [ ] Tests, Docker image and CI

## Architecture

1. **Source:** an asynchronous generator that simulates Nginx/Apache-style log lines. Labelled LogHub data will replace it so results can be measured against ground truth.
2. **Features (planned):** time-window statistics computed with sketches instead of exact counters, for example Count-Min for event frequencies, HyperLogLog for distinct counts, and Frequent Items for the most common events.
3. **Detection (planned):** an online model (River) compared against a rolling z-score baseline and an offline Isolation Forest.
4. **API:** a FastAPI service that exposes system status and flagged windows.

## Tech stack

- **API:** FastAPI, Uvicorn
- **Streaming:** Python `asyncio`
- **Sketches (planned):** Apache DataSketches (`datasketches`)
- **Online ML (planned):** River

## Quick start

```bash
pip install -r requirements.txt
uvicorn main:app --reload
```

Then open `http://127.0.0.1:8000/docs` to try the endpoints.

## Evaluation

Results will be reported here once the labelled replay and detector exist.

| Method | Dataset | Precision | Recall | F1 | Notes |
|---|---|---|---|---|---|
| Rolling z-score (baseline) | BGL (slice) | – | – | – | – |
| Isolation Forest (offline) | BGL (slice) | – | – | – | – |
| River (online) | BGL (slice) | – | – | – | – |

## Limitations

- The current log stream is simulated, so it contains no real anomalies and cannot yet be used to judge detection quality.
- Throughput and memory have not been benchmarked.

## Data and citation

Planned evaluation data comes from LogHub: *Loghub: A Large Collection of System Log Datasets for AI-driven Log Analytics* (https://arxiv.org/abs/2008.06448). Check the repository's usage terms before redistributing any data.

## How I use coding agents

I build parts of this project with AI coding agents and document what worked and what did not in `docs/ai-assisted-development.md`: what I asked, what the agent got wrong, and what I verified myself.

## Roadmap

1. Replay of labelled logs and a baseline detector
2. Sketch-based features, compared with exact counters on error and memory
3. Online detector, evaluation table, and Docker/CI
4. Stretch: raw-log parsing, a second dataset (HDFS), and instrumenting the service with OpenTelemetry
