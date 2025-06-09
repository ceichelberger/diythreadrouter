 Configuration Files

This directory contains configuration files for the various components of the Thread Border Router stack.

## Directory Structure

- `otbr/` - OpenThread Border Router configuration
  - `otbr-web.conf` - Web interface configuration

- `prometheus/` - Prometheus configuration
  - `prometheus.yml` - Main configuration file
  - `rules/` - Alert rules

- `grafana/` - Grafana configuration
  - `provisioning/` - Provisioning configuration
    - `datasources/` - Data source configurations
    - `dashboards/` - Dashboard provisioning configuration
  - `dashboards/` - Dashboard definitions

## Customizing Configurations

You can modify these configuration files to customize the behavior of the Thread Border Router stack. After making changes, restart the stack with:

```bash
task restart
```

## Adding New Configurations

If you add new configuration files, make sure to update the corresponding volume mounts in `docker-compose.yml`.
