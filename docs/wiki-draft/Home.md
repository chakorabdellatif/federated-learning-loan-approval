# Federated Learning Loan Approval — Wiki

A federated learning system for bank loan approval: three simulated banks each train a local XGBoost model on their own data and submit only model weights (never raw data) to a FastAPI aggregation server running FedAvg, producing a shared global model without any bank exposing its customers' records.

## Quick Links

- [Getting Started](Getting-Started) — prerequisites, install, and how to start the full stack
- [Architecture](Architecture) — how the federated round, Kafka streaming, and monitoring stack fit together
- [FAQ](FAQ) — common questions about the design

## Tech Stack at a Glance

- XGBoost + FedAvg aggregation, scikit-learn for evaluation
- FastAPI federated server, Python 3.9+
- Apache Kafka + Zookeeper for real-time transaction streaming
- Streamlit (ML metrics) + Prometheus/Grafana (system metrics)
- Docker Compose orchestrating all services
