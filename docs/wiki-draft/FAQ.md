# FAQ

**Why federated learning instead of just pooling all three banks' data into one training set?**
The entire point of the design is that no bank has to share its raw customer data with anyone else (or with the aggregation server) — only model weights ever leave a bank's own client. Pooling data would defeat that privacy guarantee, even if it might be simpler to implement.

**What does the Kafka layer actually contribute, since it's separate from the FedAvg training loop?**
It simulates realistic real-time transaction inflow per bank, so the dashboards and clients have something to demonstrate live inference against, independent of the hourly retraining cycle.

**What happens if I restart the stack mid-training?**
Global and local model rounds are persisted to disk under `models/global/` and `models/local/bank_{1,2,3}/`, so a restart resumes from the last completed round rather than retraining from scratch.

**Why XGBoost instead of a neural network for the local models?**
`models_testing.ipynb` compares Logistic Regression and XGBoost on this dataset directly — check that notebook for the actual metrics behind the decision.

**Can I point this at a different dataset?**
In principle yes — replace the per-bank CSVs under `data/` with your own partitions in the same schema. This hasn't been validated against datasets with a different feature schema, so expect to adjust `bank_client.py`'s feature handling.

**Do I need all of Kafka/Prometheus/Grafana running just to see the federated learning work?**
No — `docker-compose up -d` starts everything together by design, but the core federated loop (server + 3 bank clients) is functionally independent of the Kafka streaming and monitoring services if you only care about the FedAvg mechanics.
