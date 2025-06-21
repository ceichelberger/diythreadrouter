# Configuration Guide

This guide covers the configuration of all components in the DIY Thread Border Router stack.

## System Overview

The DIY Thread Border Router consists of several integrated components, each with its own configuration:

- **OpenThread Border Router** - The core Thread networking service
- **Prometheus** - Metrics collection system
- **Grafana** - Visualization and dashboarding
- **cAdvisor** - Container metrics collection
- **ESP32-H2 firmware** - Thread radio co-processor

## Directory Structure

All configuration files are located in the `configs/` directory:

```
configs/
├── otbr/
│   └── otbr-web.conf        # Web interface configuration
├── prometheus/
│   └── prometheus.yml       # Prometheus configuration
└── grafana/
    ├── dashboards/          # Dashboard definitions
    │   └── cadvisor-dashboard.json  # cAdvisor dashboard
    └── provisioning/        # Automatic provisioning configs
        ├── dashboards/      # Dashboard provisioning
        └── datasources/     # Data source provisioning
```

## OpenThread Border Router Configuration

### Web Interface Configuration

The OpenThread Border Router web interface is configured in `configs/otbr/otbr-web.conf`:

```
OTBR_WEB_LISTEN_ADDRESS="::"
OTBR_WEB_LISTEN_PORT="80"
```

This configuration makes the web interface accessible on all network interfaces on port 80.

### Thread Network Configuration

The Thread network itself is configured through the web interface or CLI after startup. See the [Operation Guide](./operation.md) for details.

## Prometheus Configuration

Prometheus is configured in `configs/prometheus/prometheus.yml`:

```yaml
global:
  scrape_interval: 15s     # How frequently to scrape targets
  evaluation_interval: 15s  # How frequently to evaluate rules

scrape_configs:
  - job_name: 'prometheus'  # Self-monitoring
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'otbr'        # Thread Border Router metrics
    static_configs:
      - targets: ['otbr:8080']
    metrics_path: /metrics

  - job_name: 'node'        # System metrics
    static_configs:
      - targets: ['prometheus-exporter:9100']

  - job_name: 'cadvisor'    # Container metrics
    static_configs:
      - targets: ['cadvisor:8080']
    metrics_path: /metrics
```

To add additional metrics sources, add new job definitions under `scrape_configs`.

## Grafana Configuration

### Data Sources

Grafana data sources are configured in `configs/grafana/provisioning/datasources/prometheus.yml`:

```yaml
apiVersion: 1

datasources:
  - name: Prometheus
    type: prometheus
    access: proxy
    url: http://prometheus:9090
    isDefault: true
```

### Dashboards

Dashboards are automatically provisioned from `configs/grafana/dashboards/` based on the configuration in `configs/grafana/provisioning/dashboards/dashboards.yml`:

```yaml
apiVersion: 1

providers:
  - name: 'Thread Network'
    orgId: 1
    folder: ''
    type: file
    disableDeletion: false
    updateIntervalSeconds: 10
    allowUiUpdates: true
    options:
      path: /var/lib/grafana/dashboards
      foldersFromFilesStructure: true
```

### Custom Dashboards

To add custom dashboards:

1. Create a JSON dashboard file in `configs/grafana/dashboards/`
2. Restart the services or wait for auto-refresh

## Docker Compose Configuration

The overall stack is configured in `docker-compose.yml`. The key services include:

- **otbr** - OpenThread Border Router service
- **prometheus** - Metrics collection service
- **grafana** - Visualization service
- **prometheus-exporter** - System metrics exporter
- **cadvisor** - Container metrics exporter

Each service's networking, volumes, and environment variables are defined in this file.

## Customizing Configurations

After modifying configuration files, restart the stack with:

```bash
task restart
```

Or restart individual services with:

```bash
docker compose restart <service-name>
```

## Advanced Configuration

### Persistent Storage

Important data is persisted in Docker volumes:

- **otbr-data** - Thread network credentials and settings
- **prometheus-data** - Historical metrics
- **grafana-data** - Dashboards and user settings

### Network Configuration

The Docker Compose network is configured to allow services to communicate while exposing necessary ports to the host:

- Port 80 - OTBR web interface
- Port 3000 - Grafana interface
- Port 8081 - Thread network visualizer
- Port 9090 - Prometheus interface (optional access)

## Next Steps

- [Operation Guide](./operation.md) - Day-to-day usage
- [Monitoring Guide](./monitoring.md) - Using the observability stack
