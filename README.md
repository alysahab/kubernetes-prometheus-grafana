# 📘 Flask Metrics Monitoring with Prometheus & Grafana

A fully containerized and monitored Flask application running on **Minikube**, with **Prometheus** scraping metrics and **Grafana** visualizing them. This README is styled for GitHub — clean, structured, and easy to follow.

---

## ⭐ Overview

This project demonstrates how to:

* Deploy a **Flask API** exposing **Prometheus metrics**
* Containerize and run it on **Minikube (Kubernetes)**
* Install **Prometheus** using Helm and configure scraping
* Install **Grafana** and connect it to Prometheus
* Build real‑time dashboards for monitoring

---

## 📂 Project Workflow

Below is the complete workflow.

---

## 🚀 1. Flask Application with Metrics

```bash
# Work locally first
python app.py
```

Your Flask app exposes metrics at `/metrics`.

---

## 🛳️ 2. Deploy Flask App on Minikube

### 🔧 Build Docker Image

```bash
docker build -t flask-metrics-app:latest .
```

### 📄 Create Kubernetes Deployment (flask-app.yaml)

### ▶ Start Minikube

```bash
minikube start
```

### 📦 Load Image into Minikube

```bash
minikube image load flask-metrics-app:latest
```

### 🚀 Deploy App

```bash
kubectl apply -f flask-app.yaml
```

### 🌐 Access App

```bash
minikube service flask-metrics-app --url
```

---

## 📡 3. Install Prometheus via Helm

### 🧰 Install Helm

Windows:

```powershell
winget install Helm.Helm
```

### ➕ Add Prometheus Repo

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

### 📥 Install Prometheus

```bash
helm install prometheus prometheus-community/prometheus -n monitoring --create-namespace
```

---

## 📊 4. Access Prometheus Dashboard

### List Services

```bash
kubectl get svc -n monitoring
```

### Port Forward

```bash
kubectl port-forward -n monitoring svc/prometheus-server 9090:80
```

➡ Open: **[http://localhost:9090](http://localhost:9090)**

---

## 🔧 5. Configure Prometheus to Scrape Flask App

### Get Service IP

```bash
kubectl get svc
```

Example:

```
10.107.61.186 - 5000:30737/TCP
```

### Edit ConfigMap

```bash
kubectl edit configmap prometheus-server -n monitoring
```

Add:

```yaml
- job_name: 'flask-app'
  static_configs:
    - targets: ['10.107.61.186:5000']
```

### 🔄 Restart Prometheus

```bash
kubectl rollout restart deployment prometheus-server -n monitoring
```

### ✔ Verify Scraping

Open Prometheus and search:

```
total_api_requests_total
```

---

## 📈 6. Install Grafana on Minikube

### Add Repo

```bash
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```

### Install

```bash
helm install grafana grafana/grafana -n monitoring --create-namespace
```

### Port Forward

```bash
kubectl port-forward svc/grafana -n monitoring 3000:80
```

➡ Open: **[http://localhost:3000](http://localhost:3000)**

---

## 🔑 7. Login to Grafana

### Get Password (Bash)

```bash
kubectl get secret -n monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode
```

### Get Password (PowerShell)

```powershell
kubectl get secret -n monitoring grafana -o jsonpath="{.data.admin-password}"
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String("<base64>"))
```

**Username:** `admin`

---

## 🔗 8. Add Prometheus as Data Source

In Grafana:

```
Connections → Data Sources → Add → Prometheus
```

Use:

```
http://prometheus-server.monitoring.svc.cluster.local:80
```

Click **Save & Test**.

---

## 📊 9. Create Dashboards

Go to:

```
Grafana → Dashboard → New Dashboard → New Panel
```

Add your PromQL queries:

* `total_api_requests_total`
* `request_latency_seconds`
* `python_info`

---

## 🏁 Final Result

You now have:

* A Flask app running in Kubernetes
* Prometheus scraping custom Python metrics
* Grafana visualizing everything with live dashboards

---

## 🧠 Command-by-Command Short Explanations

This section explains *why each command is used*.

### 🐍 Flask App & Docker

* **docker build -t flask-metrics-app****:latest**** .**
  Builds a Docker image of your Flask app so Kubernetes can run it.

### 🚀 Minikube Deployment

* **minikube start**
  Starts a local Kubernetes cluster.

* **minikube image load flask-metrics-app****:latest**
  Loads your local Docker image into Minikube (so it doesn't pull from Docker Hub).

* **kubectl apply -f flask-app.yaml**
  Deploys your Flask app Pod + Service to the cluster.

* **minikube service flask-metrics-app --url**
  Prints a URL so you can access your Flask app in the browser.

---

### 📡 Prometheus Installation & Setup

* **helm repo add prometheus-community ...**
  Adds Prometheus chart repo so Helm can install it.

* **helm install prometheus ...**
  Installs Prometheus inside your Minikube cluster.

* **kubectl get svc -n monitoring**
  Lists Prometheus-related services.

* **kubectl port-forward svc/prometheus-server ...**
  Opens Prometheus UI on your local browser.

### 🔧 Configure Prometheus Scraping

* **kubectl edit configmap prometheus-server -n monitoring**
  Opens Prometheus config so you can add your Flask app scrape target.

* **kubectl rollout restart deployment prometheus-server -n monitoring**
  Restarts Prometheus to apply your new scrape settings.

---

### 📊 Grafana Installation

* **helm repo add grafana ...**
  Adds the Grafana chart repository.

* **helm install grafana ...**
  Installs Grafana.

* **kubectl port-forward svc/grafana ...**
  Opens Grafana UI locally.

### 🔐 Grafana Login

* **kubectl get secret grafana ... | base64 decode**
  Retrieves the default Grafana admin password.

### 🔗 Add Prometheus Data Source

* Use ClusterIP URL so Grafana can query Prometheus inside Kubernetes.

---

## 🖼️ Architecture Diagram (ASCII)

```
                 +-------------------------+
                 |      Your Laptop        |
                 |  (kubectl, Helm, CLI)   |
                 +------------+------------+
                              |
                              v
                   +----------+----------+
                   |      Minikube       |
                   |  Local K8s Cluster  |
                   +----+---------+------+
                        |         |
        +---------------+         +----------------+
        |                                          |
        v                                          v
+-----------------------+            +----------------------------+
|  Flask App (Pod)      |            |   Prometheus (Pod)         |
|  Exposes /metrics --->|----------->| Scrapes Flask Metrics      |
+---------+-------------+            +-------------+--------------+
          |                                          |
          |                                          |
          |                                          v
          |                           +----------------------------+
          |                           |       Grafana (Pod)        |
          +-------------------------->| Reads Prometheus Metrics   |
                                      | Builds Dashboards          |
                                      +----------------------------+
```

