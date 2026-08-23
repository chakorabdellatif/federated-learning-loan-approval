# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

No tagged releases exist yet for this project. The sections below summarize the
project's history to date, grouped as an `[Unreleased]` baseline.

### Added
- Federated learning system for bank loan approval: three simulated banks
  (`clients/bank_client.py`) train local XGBoost models and submit weights to a
  FastAPI aggregation server (`server/federated_server.py`) running FedAvg.
- Apache Kafka + Zookeeper real-time transaction streaming pipeline
  (`kafka/producer.py`) feeding simulated bank transactions to the clients.
- Automatic hourly retraining loop and on-disk persistence of global/local
  model rounds so the system resumes without retraining from scratch on
  restart.
- Streamlit dashboard (`dashboards/streamlit_app.py`) for ML metrics
  (accuracy, AUC-ROC, F1, precision, recall) and live Kafka streaming stats.
- Prometheus + Grafana monitoring stack for system-level metrics (CPU, memory,
  disk I/O, network, container health) via JMX exporter for Kafka.
- Docker Compose orchestration for all services (Zookeeper, Kafka, federated
  server, 3 bank clients, Kafka producers, Streamlit, Prometheus, Grafana).
- `models_testing.ipynb` notebook documenting dataset partitioning and the
  Logistic Regression vs. XGBoost model comparison behind the model choice.
- Bundled, pre-partitioned datasets derived from the [Loan Approval
  Classification Dataset](https://www.kaggle.com/datasets/taweilo/loan-approval-classification-data)
  (10k records per bank, 15k for Kafka streaming tests).

### Changed
- Simplified and cleaned up `docker-compose.yml`, `federated_server.py`,
  `bank_client.py`, and `producer.py` headers/comments for readability.
- Revised the Streamlit dashboard to drop redundant Kafka metrics panels.
- Corrected the repository clone URL in the setup instructions.

### Notes
- `.gitignore` added to exclude local virtual environments, generated model
  artifacts, and logs from version control.

[Unreleased]: https://github.com/chakorabdellatif/federated-learning-loan-approval/commits/master
