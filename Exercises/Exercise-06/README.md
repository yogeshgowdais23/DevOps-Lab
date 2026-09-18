# Exercise 6: Real-Time Operations Monitoring and Alerting

## Files
- `delivery_metrics.py`
- `prometheus.yml`
- `alert_rules.yml`
- `Dockerfile`
- `Jenkinsfile`

## 1. Install the Python package
```bash
python3 -m venv venv
source venv/bin/activate
pip install prometheus-client
```

## 2. Run the metrics application
```bash
python3 delivery_metrics.py
```
Open `http://localhost:8000/metrics`.

## 3. Run Prometheus
Run from this folder in another terminal:
```bash
docker run -d --name prometheus -p 9090:9090 \
  -v "$(pwd)/prometheus.yml:/etc/prometheus/prometheus.yml:ro" \
  -v "$(pwd)/alert_rules.yml:/etc/prometheus/alert_rules.yml:ro" \
  prom/prometheus
```
Open `http://localhost:9090/targets` and check that both targets are UP.

## 4. Run Grafana
```bash
docker run -d --name grafana -p 3000:3000 grafana/grafana
```
Open `http://localhost:3000`. Add Prometheus as a data source with URL `http://host.docker.internal:9090`.

Create panels for `total_deliveries`, `pending_deliveries`, `on_the_way_deliveries`, and `average_delivery_time_sum / average_delivery_time_count`.

## 5. Check alerts
Open `http://localhost:9090/alerts`.

## 6. Jenkins
```bash
docker run -d --name jenkins -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts
```
Open `http://localhost:8080`, complete setup, and create a Pipeline job using the `Jenkinsfile`.

## Screenshots
Screenshots from the setup are in `screenshots/`.
