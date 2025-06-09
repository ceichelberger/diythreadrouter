# DIY Thread Border Router Documentation

This directory contains comprehensive documentation for setting up and operating your DIY Thread Border Router.

## Table of Contents

### Core Guides

- [Setup & Hardware Guide](./setup.md) - Getting started with hardware assembly and initial setup
- [Firmware Guide](./firmware.md) - ESP32-H2 firmware details and flashing instructions
- [Configuration Guide](./configuration.md) - Configuring all components of the system
- [Operation Guide](./operation.md) - Day-to-day operation and management

### Networking

- [Device Commissioning](./device-commissioning.md) - Adding Thread devices to your network
- [Network Management](./network-management.md) - Managing your Thread network

### Monitoring & Analysis

- [Monitoring & Observability](./monitoring.md) - Using Grafana, Prometheus and visualization tools

### Support

- [Troubleshooting](./troubleshooting.md) - Solutions for common issues

## Documentation Structure

Each guide is focused on a specific aspect of the Thread Border Router. The documentation is designed to be read in roughly the order listed above, starting with setup and ending with advanced usage.

## Task-Based Operations

This project uses a Taskfile (based on [taskfile.dev](https://taskfile.dev)) to simplify operations. Most commands are provided as tasks that can be run with `task <command>`. For a complete list of available tasks, run:

```bash
task --list
```

## Contributing to Documentation

If you find errors or would like to improve the documentation, please submit a pull request with your changes.
