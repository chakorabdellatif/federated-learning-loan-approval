# Architecture

## The federated round

1. Each bank client (`clients/bank_client.py`) trains an XGBoost model on its own local partition of the [Loan Approval Classification Dataset](https://www.kaggle.com/datasets/taweilo/loan-approval-classification-data) (10k records each for banks 1-3).
2. Each client submits only its trained model **weights** — never raw data — to the federated server.
3. The federated server (`server/federated_server.py`, FastAPI) aggregates all submitted weights using **FedAvg** into a single global model.
4. Each bank downloads the updated global model and continues local inference/training against it.
5. This cycle repeats automatically on an hourly retraining loop, with global and local model rounds persisted to disk under `models/` so the system resumes without retraining from scratch on restart.

## Real-time streaming layer

Independently of the training loop, Kafka producers (`kafka/producer.py`) stream simulated transactions from a 15k-record test set at roughly 1 transaction/second per producer into per-bank Kafka topics (coordinated via Zookeeper). Bank clients consume this stream to demonstrate real-time inference against the current model.

## Monitoring stack

- **Streamlit** (`dashboards/streamlit_app.py`) surfaces ML-facing metrics: accuracy, AUC-ROC, F1, precision, recall, and live Kafka streaming stats.
- **Prometheus** scrapes system-level metrics (CPU, memory, disk I/O, network, container health) from all services, including Kafka via a JMX exporter (`monitoring/kafka.yml`).
- **Grafana** visualizes what Prometheus collects.

## Project layout

```
federated-learning-loan-approval/
├── data/{bank1,bank2,bank3,kafka}/     # Pre-partitioned datasets
├── server/federated_server.py          # FastAPI + FedAvg aggregation
├── clients/bank_client.py              # Per-bank training + inference
├── kafka/producer.py                   # Transaction stream producers
├── dashboards/streamlit_app.py         # ML metrics dashboard
├── docker/Dockerfile.*                 # One image per service
├── monitoring/                         # Prometheus + Kafka JMX config
└── models/{global,local}/              # Persisted model rounds (generated)
```

## Why XGBoost over the alternatives considered

`models_testing.ipynb` documents the dataset partitioning and a Logistic Regression vs. XGBoost comparison that motivated the final model choice — see that notebook for the actual benchmark numbers.
