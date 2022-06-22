#step
mkdir /etc/prometheus
cd /etc/prometheus
vi docker-compose.yml
version: '3'
volumes:
  prometheus_data:
  grafana_data:

services:
  prometheus:
    image: prom/prometheus:latest
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/promtheus
    command:
     - '--config.file=/etc/prometheus/prometheus.yml'
    ports:
      - '9090:9090'
    restart: always
  node-exporter:
    image: prom/node-exporter:latest
    ports:
      - '9100:9100'
  grafana:
    image: grafana/grafana:latest
    volumes:
      - grafana_data:/var/lib/grafana
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
    depends_on:
      - prometheus
    ports:
      - "3000:3000"


vi prometheus.yml
global:
  external_labels:
    monitor: test-monitor
  scrape_interval: 5s
scrape_configs:
  -
    job_name: prometheus
    static_configs:
      -
        targets:
          - "localhost:9090"
  -
    job_name: node-exporter
    static_configs:
      -
        targets:
          - "node-exporter:9100"
