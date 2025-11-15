# Prometheus & Grafana Notes

## 1. Introduction

**Prometheus** and **Grafana** are popular tools used for **monitoring** and **visualization**.

* **Prometheus** = Monitoring + Metrics storage.
* **Grafana** = Visualization + Dashboards.

### What is Prometheus?

Prometheus is an **open-source monitoring system** that collects and stores **metrics** (numerical time‑series data). It works by **pulling data** from applications or systems.

### What is Grafana?

Grafana is a **dashboard and visualization tool** that reads data from sources like Prometheus and displays it in **charts**, **graphs**, and **alerts**.

### Main Purpose

* Track performance of systems, applications, databases, ML models, servers, etc.
* Visualize trends.
* Detect issues early.
* Set up alerts.

---

## 2. Simple Example (How They Work Together)

Let’s say you want to monitor your API.

1. Your API exposes a `/metrics` endpoint (Prometheus format).
2. Prometheus **scrapes** (pulls) metrics from that endpoint.
3. Grafana connects to Prometheus.
4. Grafana shows dashboards like:

   * API latency
   * Error rates
   * Request count

### Basic Diagram

```
Your App  ---> exposes metrics --->  Prometheus  --->  Grafana Dashboard
```

---

## 3. What Can Be Tracked?

### With Prometheus:

* CPU usage
* Memory usage
* Network traffic
* API latency
* API errors
* Database queries per second
* Queue size
* ML model metrics (accuracy, inference time)

### With Grafana:

* All Prometheus metrics
* Logs (if connected)
* Events
* Real-time system health
* Custom dashboards

---

## 4. Most Common Usage

* Server/Infrastructure monitoring (DevOps)
* Container monitoring (Docker, Kubernetes)
* Website/API monitoring
* Database performance tracking
* Alerts & notifications (Slack, Email)
* Monitoring ML model drift and prediction performance

---

# Technical Section

## 5. Prometheus in a Simple Technical View

Prometheus collects metrics in **time-series** form:

```
metric_name{label1="value1", label2="value2"} 12.4
```

Example:

```
api_request_latency_seconds{method="POST"} 0.32
```

Prometheus uses a **YAML config file** to define where to scrape metrics:

```
scrape_configs:
  - job_name: "my_api"
    static_configs:
      - targets: ["localhost:8000"]
```

---

## 6. Grafana in a Simple Technical View

* Add Prometheus as a **data source**.
* Build dashboards using panels (graphs, tables, stat boxes).
* Create alerts (e.g., if latency > 1 sec).

---

# 7. ML Example (Very Simple)

Let’s say you have an ML model deployed as an API.

### Metrics You Expose

Your ML API exposes metrics like:

* **model_inference_time_seconds**: How long the model takes to predict.
* **model_accuracy**: Daily accuracy.
* **prediction_count**: Number of predictions.
* **error_rate**: Wrong predictions.

### Simple Metrics Endpoint Example

```
model_inference_time_seconds 0.15
prediction_count 300
model_accuracy 0.91
error_rate 0.03
```

### How It Works

1. ML API exposes `/metrics`.
2. Prometheus scrapes it every 5 seconds.
3. Grafana visualizes:

   * Inference latency line chart
   * Accuracy bar chart
   * Prediction count per hour
   * Alerts if accuracy drops

### ML Diagram

```
ML Model API ---> /metrics ---> Prometheus ---> Grafana Dashboard
```

---

# 8. Why This Is Useful for ML Engineers

* Monitor model drift
* Detect performance drop
* Track latency (important for real-time apps)
* Ensure the model is healthy in production
* Visualize all metrics live
  
# 9. Quick Summary

* **Prometheus = Collects metrics**
* **Grafana = Visualizes metrics**
* Used for DevOps, ML, API monitoring, alerts
* Very helpful for production ML systems
