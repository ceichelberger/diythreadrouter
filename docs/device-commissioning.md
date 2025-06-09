# Device Commissioning Guide
# Device Commissioning Guide

This guide explains how to commission Thread devices to your DIY Thread Border Router network.

## What is Device Commissioning?

Commissioning is the process of securely adding a Thread device to your Thread network. This involves authenticating the device and providing it with the network credentials needed to join.

## Prerequisites

Before commissioning devices:

1. Ensure your Thread Border Router is running and has formed a Thread network
2. Verify the device you want to commission supports Thread
3. Have the device ready in its commissioning mode (refer to device documentation)

## Commissioning Methods

There are several ways to commission devices to your Thread network:

### 1. QR Code Commissioning

Many Thread devices provide a QR code that contains the commissioning information:

1. Access the OTBR web interface at `http://<raspberry-pi-ip>:80`
2. Click on "Commissioner"
3. Select "QR Code" method
4. Scan the QR code on your Thread device
5. The device should join the network automatically

### 2. Manual Commissioning

If a QR code is not available:

1. Access the OTBR web interface
2. Click on "Commissioner"
3. Select "Manual" method
4. Enter the device information:
   - Device EUI64 (MAC address)
   - Joining credential (usually printed on the device or in documentation)
   - Device passphrase (if required)
5. Click "Start" to begin commissioning
6. Put your device in commissioning mode (usually by pressing a button)

### 3. On-Mesh Commissioning

For Thread 1.2+ devices that support on-mesh commissioning:

1. Put the device in commissioning mode
2. The device will automatically discover the Thread Border Router
3. Follow the manufacturer's instructions to complete commissioning

## Thread Commissioning Using Mobile Apps

Some Thread devices can be commissioned using manufacturer-specific mobile apps:

1. Install the manufacturer's app on your smartphone
2. Connect your smartphone to the same Wi-Fi network as your Thread Border Router
3. Follow the in-app instructions to commission the device
4. The app will communicate with your Thread Border Router during the process

## Verifying Successful Commissioning

To verify a device has successfully joined your Thread network:

1. Check the OTBR web interface under "Topology"
2. The new device should appear in the list
3. You can also verify using the OpenThread CLI:

```bash
docker-compose exec otbr ot-ctl
> neighbor table
```

The device should appear in the neighbor table if it's within direct radio range of the Border Router.

## Troubleshooting Commissioning

If a device fails to commission:

1. Ensure the device is in commissioning mode
2. Verify you're using the correct credentials
3. Check that the device is within Thread radio range
4. Some devices may need to be reset to factory defaults before commissioning
5. Check the Thread Border Router logs for errors:

```bash
docker-compose logs otbr
```

## Managing Commissioned Devices

### Viewing Connected Devices

To view all devices on your Thread network:

1. Access the OTBR web interface
2. Click on "Topology"
3. All connected devices will be displayed

### Removing Devices

To remove a device from your Thread network:

1. Reset the device to factory defaults (refer to device documentation)
2. If necessary, you can reset the entire Thread network through the OTBR web interface under "Form" > "Reset"

## Next Steps

- [Network Management](./network-management.md)
- [Monitoring Guide](./monitoring.md)
- [Troubleshooting](./troubleshooting.md)
This guide explains how to add (commission) Thread devices to your Thread network.

## Understanding Thread Commissioning

Commissioning is the process of securely adding a new device to your Thread network by providing it with the necessary credentials. Thread supports multiple commissioning methods:

- **QR Code**: Scanning a QR code with credentials
- **On-device Button**: Using physical buttons for commissioning
- **Thread Commissioning App**: Using a mobile app

## Prerequisites

- A running Thread Border Router (see [Getting Started](../setup/hardware-guide.md))
- A Thread-enabled device to commission

## Commissioning Methods

### Method 1: Using QR Code

Many Thread devices provide a QR code that contains the necessary commissioning information:

1. Get the QR code from your Thread device (usually on a label)
2. Use a Thread commissioning app on your smartphone
3. Scan the QR code
4. Select your Thread network
5. The app will securely add the device to your network

### Method 2: Using the Border Router

The OpenThread Border Router supports direct commissioning:

1. Put your Thread device in commissioning mode (usually by pressing a button)
2. Go to the OTBR web interface at `http://<raspberry-pi-ip>`
3. Navigate to the "Commissioner" section
4. Enter the device's commissioning credentials (usually a PIN code)
5. Click "Start" to begin commissioning
6. The Border Router will securely add the device to your network

### Method 3: Using Thread Commissioning App

For mobile devices, you can use a Thread commissioning app:

1. Download a Thread commissioning app (e.g., OpenThread Commissioner)
2. Connect to your Thread Border Router
3. Follow the app's instructions to commission the device

## Verifying Successful Commissioning

After commissioning, verify the device has joined your Thread network:

1. Go to the OTBR web interface
2. Navigate to the "Topology" section
3. Your newly commissioned device should appear in the list

Alternatively, check via the REST API:

```bash
curl http://<raspberry-pi-ip>:8080/api/devices
```

## Troubleshooting

### Device Not Appearing in Network

If the device doesn't appear in your network after commissioning:

1. Ensure the device is powered on
2. Try resetting the device and commissioning again
3. Check that the device supports the Thread version used by your Border Router
4. Verify the device is within radio range of a Thread Router

### Commissioning Errors

Common commissioning errors include:

| Error | Solution |
|-------|----------|
| Invalid Credentials | Double-check the PIN code or credentials |
| Timeout | Move the device closer to the Border Router |
| Already Commissioned | Reset the device first |
| Commissioner Not Started | Restart the commissioning process |

## Thread Device Categories

Different Thread devices have different capabilities:

- **Full Thread Device (FTD)**: Can act as a Router, extending your mesh network
- **Minimal Thread Device (MTD)**: Connects as an End Device, often battery-powered
- **Sleepy End Device (SED)**: Special MTD that sleeps to conserve power

Knowing your device type helps understand its behavior in your network.

## Next Steps

- [Network Management](./network-management.md) - Manage your Thread network
- [Troubleshooting](./troubleshooting.md) - Solve common Thread network issues
