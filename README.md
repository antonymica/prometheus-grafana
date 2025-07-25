# Monitoring Stack with Prometheus, Grafana, and Node Exporter

This repository provides a `docker-compose.yml` configuration to set up a monitoring stack using **Prometheus**, **Grafana**, and **Node Exporter**. This setup allows you to collect, store, and visualize system metrics effectively.

## Overview

- **Prometheus**: A time-series database and monitoring system that collects metrics from configured targets.
- **Grafana**: A visualization platform for creating dashboards to display metrics collected by Prometheus.
- **Node Exporter**: A Prometheus exporter that collects system-level metrics (CPU, memory, disk, etc.) from the host machine.

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/) installed on your system.
- [Docker Compose](https://docs.docker.com/compose/install/) installed on your system.

## Directory Structure

Ensure the following directories exist in the project root to persist configuration and data:
- `./prometheus`: For Prometheus configuration files (e.g., `prometheus.yml`).
- `./grafana`: For Grafana provisioning files (e.g., datasource configuration).

Example structure:
```
monitoring-stack/
├── docker-compose.yml
├── prometheus/
│   └── prometheus.yml
├── grafana/
│   └── provisioning/
│       └── datasources/
│           └── datasource.yml
```

## Setup Instructions

1. **Clone the Repository**
   ```bash
   git clone <repository-url>
   cd monitoring-stack
   ```

2. **Configure Prometheus**
   Create a `prometheus.yml` file in the `./prometheus` directory. Example configuration:
   ```yaml
   global:
     scrape_interval: 15s
   scrape_configs:
     - job_name: 'prometheus'
       static_configs:
         - targets: ['localhost:9090']
     - job_name: 'node-exporter'
       static_configs:
         - targets: ['node-exporter:9100']
   ```

3. **Configure Grafana Datasource**
   Create a `datasource.yml` file in `./grafana/provisioning/datasources/`. Example:
   ```yaml
   apiVersion: 1
   datasources:
     - name: Prometheus
       type: prometheus
       url: http://prometheus:9090
       access: proxy
       isDefault: true
   ```

4. **Run the Stack**
   Start the services using Docker Compose:
   ```bash
   docker-compose up -d
   ```

5. **Access the Services**
   - **Prometheus**: Open `http://localhost:9090` in your browser.
   - **Grafana**: Open `http://localhost:3000` (default login: `admin` / `grafana`).
   - **Node Exporter**: Metrics available at `http://localhost:9100/metrics`.

## Service Details

### Prometheus
- **Image**: `prom/prometheus`
- **Port**: `9090`
- **Volumes**:
  - `./prometheus:/etc/prometheus`: Mounts the configuration directory.
  - `prom_data:/prometheus`: Persists Prometheus data.
- **Restart Policy**: Restarts unless manually stopped.

### Grafana
- **Image**: `grafana/grafana`
- **Port**: `3000`
- **Environment Variables**:
  - `GF_SECURITY_ADMIN_USER`: Admin username (default: `admin`).
  - `GF_SECURITY_ADMIN_PASSWORD`: Admin password (default: `grafana`).
- **Volumes**:
  - `./grafana:/etc/grafana/provisioning/datasources`: Mounts datasource configuration.
- **Restart Policy**: Restarts unless manually stopped.

### Node Exporter
- **Image**: `prom/node-exporter`
- **Port**: `9100`
- **Volumes**:
  - `/proc:/host/proc:ro`: Read-only access to host's `/proc`.
  - `/sys:/host/sys:ro`: Read-only access to host's `/sys`.
  - `/:/rootfs:ro`: Read-only access to host's root filesystem.
- **Command**: Configures paths for system metrics collection.
- **Restart Policy**: Restarts unless manually stopped.

## Stopping the Stack
To stop the services:
```bash
docker-compose down
```

To stop and remove volumes (data will be lost):
```bash
docker-compose down -v
```

## Notes
- Ensure the `prometheus.yml` and Grafana datasource configurations are correctly set up before starting the stack.
- Modify the `GF_SECURITY_ADMIN_PASSWORD` environment variable in the `docker-compose.yml` for security in production environments.
- The `prom_data` volume persists Prometheus data across container restarts.

## Troubleshooting
- **Prometheus not showing metrics**: Verify the `prometheus.yml` configuration and ensure targets (e.g., `node-exporter:9100`) are reachable.
- **Grafana login issues**: Check the environment variables for correct credentials.
- **Node Exporter errors**: Ensure the host paths (`/proc`, `/sys`, `/`) are accessible.

## Contributing
Feel free to submit issues or pull requests to improve this setup.

## License
This project is licensed under the MIT License.