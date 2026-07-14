# ⚡ On-Premise IoT Data Migration to AWS Cloud (End-to-End Pipeline)

[![AWS Cloud](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazon-aws&logoColor=white)]()
[![Snowflake](https://img.shields.io/badge/Snowflake-29B5E8?style=for-the-badge&logo=snowflake&logoColor=white)]()
[![dbt](https://img.shields.io/badge/dbt-FF694B?style=for-the-badge&logo=dbt&logoColor=white)]()
[![Streamlit](https://img.shields.io/badge/Streamlit-FF4B4B?style=for-the-badge&logo=streamlit&logoColor=white)]()

---

## 🟢 Live Pipeline Status 
| Component | Status | Metrics (Simulated Live) |
| :--- | :--- | :--- |
| **AWS IoT Core Ingestion** | ![Active](https://img.shields.io/badge/Active-brightgreen?style=flat-square) | `~25 events/sec` |
| **Apache Kafka MSK** | ![Healthy](https://img.shields.io/badge/Healthy-brightgreen?style=flat-square) | `Topic: cdc.public.iot_events` |
| **Debezium CDC (Postgres)** | ![Streaming](https://img.shields.io/badge/Streaming-brightgreen?style=flat-square) | `Latency: < 500ms` |
| **Snowflake Warehouse** | ![Online](https://img.shields.io/badge/Online-blue?style=flat-square) | `HACKATHON_IOT.RAW` |
| **dbt Transformation** | ![Passing](https://img.shields.io/badge/Models_Passing-brightgreen?style=flat-square) | `Bronze ➡️ Silver ➡️ Gold` |

---

## 📌 Project Overview
This project simulates a high-scale enterprise scenario: migrating real-time on-premise IoT sensor telemetry data (stored in a local PostgreSQL database) into a modern cloud data platform (Snowflake) via change-data-capture (CDC) pipelines. 

The entire cloud infrastructure is provisioned programmatically using **AWS CDK (Python)**.

---

## 🏗️ System Architecture

The pipeline runs in two distinct phases:

### Phase 1: IoT Ingestion & On-Premise Simulation
*   **Data Generation:** **AWS IoT Device Simulator** mimics 5+ virtual devices emitting geolocation data (Latitude, Longitude, Device ID, Timestamp) around the London 02 Arena.
*   **Ingestion:** Sent via **MQTT** protocol through **AWS IoT Core**.
*   **Streaming & Storage:** Routed to **Apache Kafka (AWS MSK)**, then written to **PostgreSQL on EC2** (acting as our On-Prem DB) via **Kafka Connect JDBC Sink**.

### Phase 2: CDC Migration, Snowflake Warehouse & dbt Analytics
*   **Migration (CDC):** **Debezium CDC** tracks changes in PostgreSQL WAL logs in real-time, pushing event changes back to **Kafka MSK**[cite: 3].
*   **Data Warehouse Ingestion:** **Snowflake Kafka Connector** streams the change events directly into Snowflake (Bronze Layer)[cite: 3].
*   **Data Transformation:** **dbt (data build tool)** parses, cleans, and runs SQL models to build Silver (`CLEAN.IOT_VALIDATED`) and Gold (`ANALYTICS.DEVICE_DAILY`) layers[cite: 3].
*   **Visualizations:**
    *   **Streamlit Dashboard:** Queries the Snowflake Gold layer to show live maps, AQI trends, and active telemetry[cite: 3].
    *   **Grafana Dashboard:** Visualizes extreme time-series data using **AWS Timestream**[cite: 3].

---

## 📂 Project Structure
```bash
├── Architecture Of Project/             # Architecture diagrams (PNG/PDF)
├── AWS CDK/                             # Infrastructure as Code (Python Stacks)
├── Python Code/                         # Device simulators & helper scripts
├── Snowflake Queries/                   # SQL scripts for Bronze, Silver, Gold
├── dbt_project/                         # dbt models and schemas
├── Streamlit Visualization/             # Streamlit web app (app.py)
└── README.md                            # This documentation file
