# 📊 Monitoring a Linux Host with Prometheus, Node Exporter, and Grafana using Docker Compose
This guide sets up a complete monitoring stack using Docker Compose — including Prometheus for metrics collection, Node Exporter for Linux host metrics, and Grafana for visualization.

## 📁 Project Structure
```text
monitoring-stack/
├── docker-compose.yml
├── prometheus.yml
```
---
## 🐳 1. Docker Compose Setup
### docker-compose.yml
Create the following docker-compose.yml file in your project root:
```bash
version: '3.9'

networks:
  monitoring:
    driver: bridge

volumes:
  prometheus_data: {}

services:
  node-exporter:
    image: prom/node-exporter:latest
    container_name: node-exporter
    restart: unless-stopped
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - '--path.procfs=/host/proc'
      - '--path.rootfs=/rootfs'
      - '--path.sysfs=/host/sys'
      - '--collector.filesystem.mount-points-exclude=^/(sys|proc|dev|host|etc)($$|/)'
    expose:
      - 9100
    networks:
      - monitoring

  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    restart: unless-stopped
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/etc/prometheus/console_libraries'
      - '--web.console.templates=/etc/prometheus/consoles'
      - '--web.enable-lifecycle'
    expose:
      - 9090
    networks:
      - monitoring

  grafana:
    image: grafana/grafana
    container_name: grafana
    restart: unless-stopped
    ports:
      - "3000:3000"
    networks:
      - monitoring
```
---

## ⚙️ 2. Prometheus Configuration
### prometheus.yml
Create the prometheus.yml file in the same directory:
```bash
global:
  scrape_interval: 5s

scrape_configs:
  - job_name: 'node'
    static_configs:
      - targets: ['node-exporter:9100']
```
---
## 🚀 3. Deployment Steps
### ✅ Step 1: Clean up old containers (if any)
```bash
docker rm -f $(docker ps -aq)
```
### ✅ Step 2: Start all services with Docker Compose
```bash
docker compose up -d
```
### ✅ Step 3: Access Grafana in browser
1. http://localhost:3000

2. Or use your server IP: http://<server-ip>:3000

#### 🔐 Default credentials for Grafana:

Username: admin

Password: admin (you'll be prompted to change this)

## 📡 4. Connect Prometheus as Grafana Data Source
1. Go to Grafana > Settings > Data Sources

2. Click "Add data source"

3. Choose Prometheus

4. Set URL to: http://prometheus:9090

5. Click save & test

## 📈 5. Import Node Exporter Dashboard in Grafana
1. Go to Dashboards > Import

2. Enter Node Exporter template ID (e.g., 1860 — Node Exporter Full)

3. Click Load

4. Choose your Prometheus data source

5. Click Import

## 🎉 We’re Done!
We now have:

1. Node Exporter collecting system metrics

2. Prometheus scraping metrics

3. Grafana displaying dashboards


---


