# Monitoring & Observability Guide

This guide covers the monitoring and observability features of the DIY Thread Border Router, including Prometheus metrics, Grafana dashboards, and network visualization.

## Overview

The monitoring stack consists of several components:

- **Prometheus** - Collects and stores metrics
- **Grafana** - Visualizes metrics in dashboards
- **Thread Visualizer** - Provides a graphical view of the Thread network topology
- **Node Exporter** - Collects system-level metrics from the Raspberry Pi

## Accessing Monitoring Interfaces

### Grafana Dashboards

Grafana is available at:

```
http://<raspberry-pi-ip>:3000
```

Default login credentials:
- Username: admin
- Password: admin

You will be prompted to change the password on first login.

**Success Criteria**: You should see the Grafana login page when you access the URL, and after logging in, you should see the main Grafana interface with the pre-configured dashboards available.

### Thread Network Visualizer

The Thread Network Visualizer is available at:

```
http://<raspberry-pi-ip>:8081
```

This provides a real-time graphical view of your Thread network topology, including device connections and roles.

**Success Criteria**: When accessed, you should see a graphical representation of your Thread network. If you have an active Thread network, you'll see connected devices represented as nodes in the visualization.

### Prometheus Interface (Optional)

The Prometheus interface is available at:

```
http://<raspberry-pi-ip>:9090
```

This is primarily useful for advanced users who want to query metrics directly or debug the monitoring system.

## Available Dashboards

### Thread Network Overview

This dashboard provides a high-level overview of your Thread network, including:

- Network status and uptime
- Device count and roles
- Network traffic metrics
- Key performance indicators

### System Performance

This dashboard monitors the performance of the Raspberry Pi itself:

- CPU, memory, and disk usage
- Network interface throughput
- System load and process statistics
- Temperature and power metrics

## OTBR Metrics

The OpenThread Border Router exposes metrics on port 8080 at the `/metrics` endpoint. These metrics include:

- Thread network status
- Connected device counts
- Network performance metrics
- Error counters

## Creating Custom Dashboards

To create custom dashboards:

1. Login to Grafana
2. Click the "Create" (plus) icon in the sidebar
3. Select "Dashboard"
4. Add panels as needed, using Prometheus as the data source
5. Save your dashboard

**Success Criteria**: After saving, your new dashboard should appear in the dashboard list and display the configured metrics properly.

### Adding Custom Panels

To add a custom panel to a dashboard:

1. Click "Add panel"
2. Select "Add new panel"
3. Choose visualization type (graph, gauge, etc.)
4. Configure the query using PromQL (Prometheus Query Language)
5. Set display options
6. Click "Save"

## Useful PromQL Queries

Here are some useful Prometheus queries for Thread network monitoring:

- Thread device count: `otbr_thread_device_count`
- Router device count: `otbr_thread_router_count`
- Network traffic: `rate(otbr_thread_bytes_total[5m])`
- System CPU usage: `100 - (avg by (instance) (irate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)`

## Alerting

Grafana can be configured to send alerts when certain conditions are met:

1. Edit a panel in a dashboard
2. Go to the "Alert" tab
3. Define alert conditions
4. Configure notification channels (email, webhook, etc.)

Common alert conditions:

- Thread network disconnection
- High CPU or memory usage
- Abnormal network traffic patterns

## Troubleshooting Monitoring

If metrics are not appearing in Grafana:

1. Check that all containers are running: `docker-compose ps`
2. Verify Prometheus can reach targets: `http://<raspberry-pi-ip>:9090/targets`
3. Check Prometheus logs: `docker-compose logs prometheus`
4. Verify OTBR metrics endpoint: `curl http://localhost:8080/metrics`

## Next Steps

- [Troubleshooting Guide](./troubleshooting.md)
- [Device Commissioning](./device-commissioning.md)
