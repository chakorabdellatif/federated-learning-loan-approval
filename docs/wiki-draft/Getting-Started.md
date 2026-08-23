# Getting Started

## Prerequisites

- Docker >= 20.10 and Docker Compose >= 2.0
- Git
- At least 8 GB RAM and 10 GB free disk space available to Docker

## Install and run

```bash
git clone https://github.com/chakorabdellatif/federated-learning-loan-approval.git
cd federated-learning-loan-approval

# Verify the bundled datasets are present
ls data/bank1/bank1_dataset.csv
ls data/bank2/bank2_dataset.csv
ls data/bank3/bank3_dataset.csv
ls data/kafka/real_time_testing_dataset.csv

# Start everything
docker-compose up -d
docker-compose ps
```

## Startup order

The stack brings itself up in this order automatically — full startup takes roughly 2-3 minutes:

1. Zookeeper → Kafka (coordination)
2. Federated server (aggregation)
3. Bank clients (initial local training → Round 0)
4. Kafka producers (wait for Round 0 to finish before streaming)
5. Streamlit & Grafana dashboards

## Verifying it's running

```bash
docker logs federated-server -f
docker logs bank1-client -f
docker logs kafka -f
```

## Running CI checks locally

CI lints and syntax-checks each service and validates `docker-compose.yml`:

```bash
pip install -r server/requirements.txt -r clients/requirements.txt -r kafka/requirements.txt -r dashboards/requirements.txt
pip install flake8
python -m py_compile server/*.py clients/*.py kafka/*.py dashboards/*.py
python -m flake8 --max-line-length=120 --select=E9,F63,F7,F82 server clients kafka dashboards
docker compose config -q
```
