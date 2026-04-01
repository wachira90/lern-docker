# **Docker/cAdvisor/Node Exporter → Prometheus → Grafana**

That gives you:

* **Docker daemon metrics** from `dockerd`
* **Container metrics** from **cAdvisor**
* **Host metrics** from **node-exporter**
* **Dashboards** in **Grafana** via **Prometheus** scraping those endpoints. Docker’s docs show Prometheus scraping Docker daemon metrics, and Prometheus docs show cAdvisor for container metrics. ([Docker Documentation][1])

## 1) Enable Docker daemon metrics

On the Docker host, edit `/etc/docker/daemon.json`:

```json
{
  "metrics-addr": "0.0.0.0:9323",
  "experimental": true
}
```

Then restart Docker:

```bash
sudo systemctl restart docker
```

Docker’s official docs describe exposing metrics on `9323` and note that enabling metrics requires the experimental feature flag. ([Docker Documentation][1])

## 2) Run Prometheus + Grafana + cAdvisor + node-exporter

Create this `docker-compose.yml`:

```yaml
version: "3.8"

services:
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    command:
      - --config.file=/etc/prometheus/prometheus.yml
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml:ro
    ports:
      - "9090:9090"
    restart: unless-stopped

  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    ports:
      - "3000:3000"
    volumes:
      - grafana-data:/var/lib/grafana
    restart: unless-stopped

  cadvisor:
    image: gcr.io/cadvisor/cadvisor:latest
    container_name: cadvisor
    ports:
      - "8080:8080"
    volumes:
      - /:/rootfs:ro
      - /var/run:/var/run:ro
      - /sys:/sys:ro
      - /var/lib/docker:/var/lib/docker:ro
    privileged: true
    restart: unless-stopped

  node-exporter:
    image: prom/node-exporter:latest
    container_name: node-exporter
    ports:
      - "9100:9100"
    restart: unless-stopped

volumes:
  grafana-data:
```

Prometheus officially documents cAdvisor as the standard way to collect Docker container metrics, and Grafana documents Prometheus-based Docker monitoring with node-exporter for host metrics. ([Prometheus][2])

## 3) Prometheus scrape config

Create `prometheus.yml` in the same directory:

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['prometheus:9090']

  - job_name: 'docker'
    static_configs:
      - targets: ['host.docker.internal:9323']

  - job_name: 'cadvisor'
    static_configs:
      - targets: ['cadvisor:8080']

  - job_name: 'node'
    static_configs:
      - targets: ['node-exporter:9100']
```

On **Linux**, `host.docker.internal` may not resolve automatically. If needed, replace it with the host IP, or add:

```yaml
extra_hosts:
  - "host.docker.internal:host-gateway"
```

under the `prometheus` service. Docker’s docs use Prometheus scraping the Docker metrics endpoint, while cAdvisor and node-exporter expose separate scrape targets for container and host data. ([Docker Documentation][1])

## 4) Start the stack

```bash
docker compose up -d
```

Then open:

* Grafana: `http://localhost:3000`
* Prometheus: `http://localhost:9090`

Grafana’s default login is commonly `admin / admin` on first start for the official container image. ([Grafana Labs][3])

## 5) Add Prometheus as Grafana data source

In Grafana:

* Go to **Connections / Data sources**
* Add **Prometheus**
* URL: `http://prometheus:9090`

Docker’s Grafana/Prometheus guide uses Prometheus as the metrics source that Grafana visualizes. ([Docker Documentation][4])

## 6) Import Docker dashboards

Useful ready-made dashboards:

* **Docker container monitoring with Prometheus and cAdvisor**: dashboard ID **19908**
* **Docker and system monitoring**: dashboard ID **13496** ([Grafana Labs][5])

## What each source gives you

* **dockerd metrics**: engine-level metrics such as daemon activity and builder/runtime internals. ([Docker Documentation][1])
* **cAdvisor**: per-container CPU, memory, filesystem, and network metrics. Prometheus’ guide shows metrics like `container_cpu_usage_seconds_total`, `container_memory_usage_bytes`, and network byte counters. ([Prometheus][2])
* **node-exporter**: host CPU, RAM, disk, filesystem, load, and network metrics. ([Grafana Labs][3])

## Quick Prometheus queries to test

```promql
up
```

```promql
rate(container_cpu_usage_seconds_total[1m])
```

```promql
container_memory_usage_bytes
```

```promql
rate(container_network_receive_bytes_total[1m])
```

These container metric names and example queries are documented in Prometheus’ cAdvisor guide. ([Prometheus][2])

## Best practice

For real monitoring, use all three:

* **dockerd** for Docker engine health
* **cAdvisor** for container usage
* **node-exporter** for server health

That combination is the most practical baseline for Grafana dashboards in Docker environments. ([Docker Documentation][1])

If you want, I can give you a **production-ready Docker Compose stack** with persistent volumes, provisioning, and preloaded Grafana dashboards.

[1]: https://docs.docker.com/engine/daemon/prometheus/?utm_source=chatgpt.com "Collect Docker metrics with Prometheus | Docker Docs"
[2]: https://prometheus.io/docs/guides/cadvisor/?utm_source=chatgpt.com "Monitoring Docker container metrics using cAdvisor - Prometheus"
[3]: https://grafana.com/docs/grafana-cloud/send-data/metrics/metrics-prometheus/prometheus-config-examples/docker-compose-linux/?utm_source=chatgpt.com "Monitoring a Linux host with Prometheus, Node Exporter, and Docker ..."
[4]: https://docs.docker.com/guides/go-prometheus-monitoring/?utm_source=chatgpt.com "Monitor with Prometheus and Grafana | Docker Docs"
[5]: https://grafana.com/grafana/dashboards/19908-docker-container-monitoring-with-prometheus-and-cadvisor/?utm_source=chatgpt.com "cAdvisor Docker Insights - Grafana Labs"

---

## 🧠 แนวคิดหลัก

โครงสร้างมาตรฐานสำหรับ monitor Docker คือ:

**Docker / cAdvisor / node-exporter → Prometheus → Grafana**

* **Docker daemon** → metrics ของ engine
* **cAdvisor** → metrics ของ container
* **node-exporter** → metrics ของเครื่อง (CPU, RAM, Disk)
* **Prometheus** → เก็บ metrics
* **Grafana** → ทำ dashboard

---

## 1) เปิด Docker metrics

แก้ไฟล์:

```bash
/etc/docker/daemon.json
```

ใส่:

```json
{
  "metrics-addr": "0.0.0.0:9323",
  "experimental": true
}
```

แล้ว restart:

```bash
sudo systemctl restart docker
```

---

## 2) ใช้ docker-compose รันทุกอย่าง

สร้างไฟล์ `docker-compose.yml`

```yaml
version: "3.8"

services:
  prometheus:
    image: prom/prometheus
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - "9090:9090"

  grafana:
    image: grafana/grafana
    ports:
      - "3000:3000"

  cadvisor:
    image: gcr.io/cadvisor/cadvisor
    ports:
      - "8080:8080"
    volumes:
      - /:/rootfs:ro
      - /var/run:/var/run:ro
      - /sys:/sys:ro
      - /var/lib/docker:/var/lib/docker:ro
    privileged: true

  node-exporter:
    image: prom/node-exporter
    ports:
      - "9100:9100"
```

---

## 3) config Prometheus

สร้างไฟล์ `prometheus.yml`

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: docker
    static_configs:
      - targets: ['host.docker.internal:9323']

  - job_name: cadvisor
    static_configs:
      - targets: ['cadvisor:8080']

  - job_name: node
    static_configs:
      - targets: ['node-exporter:9100']
```

📌 ถ้า Linux ใช้ `host.docker.internal` ไม่ได้ → ใช้ IP host แทน

---

## 4) รันระบบ

```bash
docker compose up -d
```

เข้าใช้งาน:

* Grafana → [http://localhost:3000](http://localhost:3000)
* Prometheus → [http://localhost:9090](http://localhost:9090)

---

## 5) ตั้งค่า Grafana

* ไปที่ **Data Sources**
* เพิ่ม **Prometheus**
* URL:

```bash
http://prometheus:9090
```

---

## 6) Import Dashboard สำเร็จรูป

ใน Grafana → Import Dashboard

แนะนำ:

* ID: **19908** → Docker + cAdvisor
* ID: **13496** → Docker + System

---

## 🔍 ตัวอย่าง metrics ที่ได้

**Container**

* CPU: `container_cpu_usage_seconds_total`
* Memory: `container_memory_usage_bytes`
* Network: `container_network_receive_bytes_total`

**Host**

* CPU / RAM / Disk จาก node-exporter

---

## 🧪 Query ทดสอบ

```promql
up
```

```promql
rate(container_cpu_usage_seconds_total[1m])
```

```promql
container_memory_usage_bytes
```

