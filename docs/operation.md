# Operation Guide

This guide covers the day-to-day operation of your DIY Thread Border Router, including starting and stopping services, creating and managing Thread networks, and basic maintenance tasks.

## Starting and Stopping Services

### Starting All Services

To start the entire Thread Border Router stack:

```bash
task start
```

This command will start all services defined in the `docker-compose.yml` file.

**Success Criteria**: All services should start without errors, and you should be able to access the web interfaces. Check the status with `docker-compose ps` to verify all containers are in the "Up" state.

### Stopping All Services

To stop all services:

```bash
task stop
```

### Restarting Services

To restart all services (useful after configuration changes):

```bash
task restart
```

### Checking Service Status

To check the status of all services:

```bash
docker-compose ps
```

### Viewing Logs

To view logs from all services:

```bash
docker-compose logs
```

Or for a specific service:

```bash
docker-compose logs otbr
```

Add `-f` to follow logs in real-time:

```bash
docker-compose logs -f otbr
```

## Thread Network Management

### Accessing the Web Interface

The OpenThread Border Router web interface is available at:

```
http://<raspberry-pi-ip>:80
```

Replace `<raspberry-pi-ip>` with your Raspberry Pi's IP address or hostname.

### Testing Device Connectivity

To check connectivity with all Thread devices on your network:

```bash
task ping-thread-devices
```

This will automatically discover all Thread devices, determine their IPv6 addresses, and ping each one to verify connectivity.

### Creating a Thread Network

1. Access the OTBR web interface
2. Click on "Form" to create a new Thread network
3. Configure network settings if desired (or use defaults)
4. Click "Form" to create the network
5. Save the network credentials for backup purposes

### Joining an Existing Thread Network

1. Access the OTBR web interface
2. Click on "Join" 
3. Enter the network credentials
4. Click "Join" to connect to the network

### Managing Thread Network via CLI

For advanced management, you can use the OpenThread CLI:

```bash
docker-compose exec otbr ot-ctl
```

Common commands include:

```
ifconfig           # View interface status
state              # View Thread state
networkname        # View/set network name
networkkey         # View network key
ipaddr             # View IP addresses
ping <ip-address>  # Ping a Thread device
exit              # Exit the CLI
```

## System Maintenance

### Updating the System

To update the underlying Raspberry Pi OS:

```bash
sudo apt update && sudo apt upgrade -y
```

### Updating Docker Images

To update the Docker images used by the project:

```bash
task update
```

### Backing Up Thread Network Credentials

Backup your Thread network credentials for recovery purposes:

```bash
docker-compose exec otbr ot-ctl dataset active -x > thread-network-backup.txt
```

### Restoring Thread Network Credentials

To restore from a backup:

```bash
# Stop services
task stop

# Start only OTBR service
docker-compose up -d otbr

# Restore network
cat thread-network-backup.txt | docker-compose exec -T otbr bash -c 'ot-ctl dataset set active -x -'
docker-compose exec otbr ot-ctl thread start

# Start remaining services
task start
```

## Security Maintenance

### Changing Default Passwords

Change the default Grafana admin password (admin/admin) by accessing:

```
http://<raspberry-pi-ip>:3000
```

Upon first login, you'll be prompted to change the password.

### Enabling Firewall

For improved security, configure a basic firewall:

```bash
sudo apt install ufw
sudo ufw allow ssh
sudo ufw allow 80/tcp   # OTBR web interface
sudo ufw allow 3000/tcp # Grafana
sudo ufw allow 8081/tcp # Thread visualizer
sudo ufw enable
```

## Next Steps

- [Device Commissioning](./device-commissioning.md) - Adding devices to your Thread network
- [Monitoring Guide](./monitoring.md) - Using the observability features
- [Troubleshooting Guide](./troubleshooting.md) - If you encounter issues
