# Thread Network Management
# Thread Network Management Guide

This guide covers advanced management of your Thread network, including configuration, optimization, and troubleshooting network-specific issues.

## Thread Network Configuration

### Network Parameters

The following parameters can be configured for your Thread network:

| Parameter | Description | Default |
|-----------|-------------|--------|
| Network Name | Human-readable name | "OpenThread Network" |
| PAN ID | Personal Area Network ID | Random |
| Extended PAN ID | 64-bit network identifier | Random |
| Network Key | Security credential | Random |
| Channel | Radio channel (11-26) | 15 |
| Network Prefix | IPv6 prefix for the network | fd00::/8 |

### Configuring Parameters

Parameters can be configured through:

1. **Web Interface**:
   - Access `http://<raspberry-pi-ip>:80`
   - Click "Form" for new network or "Settings" for existing network
   - Modify parameters as needed

2. **Command Line**:
   ```bash
   docker-compose exec otbr ot-ctl
   > networkname MyThreadNetwork
   > channel 20
   > panid 0x1234
   > extpanid 1122334455667788
   > networkkey 00112233445566778899aabbccddeeff
   ```

### Backing Up Network Configuration

Backup your Thread network configuration:

```bash
# Save complete dataset
docker-compose exec otbr ot-ctl dataset active -x > thread-network-backup.txt

# Save human-readable settings
docker-compose exec otbr ot-ctl dataset active > thread-network-settings.txt
```

### Restoring Network Configuration

Restore from a backup:

```bash
cat thread-network-backup.txt | docker-compose exec -T otbr bash -c 'ot-ctl dataset set active -x -'
docker-compose exec otbr ot-ctl thread start
```

## Network Optimization

### Channel Selection

The default channel (15) may have interference from other devices. To find a cleaner channel:

1. Scan for interference:
   ```bash
   docker-compose exec otbr ot-ctl scan
   ```

2. Select a channel with less energy detected:
   ```bash
   docker-compose exec otbr ot-ctl channel 20
   ```

### Radio Transmit Power

Adjusting transmit power can improve range or reduce interference:

```bash
# View current transmit power
docker-compose exec otbr ot-ctl txpower

# Set transmit power (0-20 dBm, depending on hardware support)
docker-compose exec otbr ot-ctl txpower 10
```

### Router Selection

Control the number of router devices allowed on your network:

```bash
# View current router threshold
docker-compose exec otbr ot-ctl routerselectionjitter

# Set router selection jitter (higher values can reduce network changes)
docker-compose exec otbr ot-ctl routerselectionjitter 120
```

## Network Monitoring

### Thread Network Status

Check basic network status:

```bash
docker-compose exec otbr ot-ctl state
```

Output will show if the network is operational ("leader" or "router").

### Viewing Connected Devices

List directly connected devices:

```bash
docker-compose exec otbr ot-ctl neighbor table
```

List all devices on the network:

```bash
docker-compose exec otbr ot-ctl neighbor list
```

### Checking Device Roles

View device roles (router, child, etc.):

```bash
docker-compose exec otbr ot-ctl router table
```

### Network Traffic

Monitor network counters:

```bash
docker-compose exec otbr ot-ctl counters
```

## Network Security

### Commissioner Credentials

The Commissioner is used to securely add devices to the network. Configure the Commissioner password:

```bash
docker-compose exec otbr ot-ctl commissioner start
docker-compose exec otbr ot-ctl commissioner joiner add * PASSPHRASE 600
```

Replace `PASSPHRASE` with your chosen credentials.

### Network Key Rotation

Periodically rotating your network key improves security:

1. Generate a new key (must be 32 hex characters):
   ```bash
openssl rand -hex 16
   ```

2. Set the new key:
   ```bash
docker-compose exec otbr ot-ctl networkkey NEW_KEY
   ```

## Advanced Network Management

### IPv6 Prefix Configuration

Configure the IPv6 prefix for your Thread network:

```bash
docker-compose exec otbr ot-ctl prefix add fd00:1111:2222:3333::/64 pasor
```

### DNS Configuration

Configure DNS servers for Thread devices:

```bash
docker-compose exec otbr ot-ctl dns config
docker-compose exec otbr ot-ctl dns server add 2001:4860:4860::8888
```

### Border Router Advertisement

Configure Border Router advertisement settings:

```bash
docker-compose exec otbr ot-ctl br status
docker-compose exec otbr ot-ctl br enable
```

## Network Troubleshooting

### Ping Test

Test connectivity to a Thread device:

```bash
docker-compose exec otbr ot-ctl ping fd00:1111:2222:3333:4444:5555:6666:7777
```

### Network Diagnostics

Run network diagnostics:

```bash
docker-compose exec otbr ot-ctl diag start
docker-compose exec otbr ot-ctl diag send 10 100
docker-compose exec otbr ot-ctl diag stop
```

### Factory Reset

If all else fails, perform a factory reset:

```bash
docker-compose exec otbr ot-ctl factoryreset
```

**Note**: This will erase all network settings.

## Next Steps

- [Device Commissioning](./device-commissioning.md) - Adding devices to your network
- [Monitoring Guide](./monitoring.md) - Monitoring network performance
- [Troubleshooting Guide](./troubleshooting.md) - General troubleshooting tips
This guide explains how to create, configure, and manage your Thread network using the OpenThread Border Router.

## Accessing the Border Router Interface

The OpenThread Border Router provides a web interface for network management:

1. Open your web browser
2. Navigate to `http://<raspberry-pi-ip>` (default port 80)

## Creating a New Thread Network

### Using the Web Interface

1. Go to the OTBR web interface
2. Click on the "Form" button
3. Configure your network parameters:
   - Network Name: A friendly name for your Thread network
   - On-Mesh Prefix: The IPv6 prefix for your network (default: `fd11:22::/64`)
   - PAN ID: Personal Area Network ID (default: random)
   - Extended PAN ID: Extended identifier (default: random)
   - Network Key: Security key (default: random)
   - Channel: Radio channel (11-26, default: automatic selection)
4. Click "Form" to create the network

### Using the REST API

You can also create a network programmatically via the REST API:

```bash
curl -X POST http://<raspberry-pi-ip>:8080/api/network \
  -H "Content-Type: application/json" \
  -d '{
    "networkName": "MyThreadNetwork",
    "extPanId": "1111111122222222",
    "panId": "0x1234",
    "channel": 15,
    "passphrase": "J01NME"
  }'
```

## Joining an Existing Thread Network

1. Obtain the network credentials (usually via QR code or manual entry)
2. Go to the OTBR web interface
3. Click on the "Join" button
4. Enter the network credentials
5. Click "Join" to connect to the network

## Managing Connected Devices

### Viewing Connected Devices

1. Go to the OTBR web interface
2. Navigate to the "Topology" section
3. This shows all devices currently connected to your Thread network

### Device Roles

Thread devices can have different roles:

- **Router**: Forwards traffic and maintains mesh connections
- **End Device**: Connects to a Router, can sleep to save power
- **Leader**: Special Router that manages network parameters
- **Border Router**: Connects the Thread network to other networks

## Network Diagnostics

### Basic Connectivity Test

Test connectivity to a Thread device (replace `<THREAD-DEVICE-IP>` with actual IPv6 address):

```bash
ping <THREAD-DEVICE-IP>
```

### Thread Network Information

View detailed network information via REST API:

```bash
curl http://<raspberry-pi-ip>:8080/api/network
```

### Network Visualization

For a graphical view of your Thread network:

1. Open your web browser
2. Navigate to `http://<raspberry-pi-ip>:8081`
3. The Thread Visualizer will show your network topology

## Backing Up Network Credentials

It's important to back up your Thread network credentials:

```bash
# Export network credentials
curl http://<raspberry-pi-ip>:8080/api/network > thread-network-backup.json
```

Store this file securely as it contains sensitive information.

## Next Steps

- [Device Commissioning Guide](./device-commissioning.md) - Add new devices to your Thread network
- [Monitoring Guide](../monitoring/monitoring-guide.md) - Monitor your Thread network's performance
