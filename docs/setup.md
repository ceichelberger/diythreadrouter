# Setup & Hardware Guide

This guide walks you through setting up the hardware and initial software configuration for your DIY Thread Border Router.

## Hardware Requirements

- **Raspberry Pi 5** (4GB+ RAM recommended)
- **ESP32-H2 development board** (ESP32-H2-DevKitC or equivalent)
- **USB-to-UART adapter** (if not built into ESP32-H2 board)
- **Power supply** for Raspberry Pi (3A+ recommended)
- **Ethernet cable** (for internet connectivity)
- **MicroSD card** (16GB+ recommended)

## Hardware Assembly

### 1. Connect ESP32-H2 to Raspberry Pi

| ESP32-H2 Pin | Raspberry Pi Pin | Function |
|--------------|------------------|----------|
| VCC (3.3V)   | Pin 1 (3.3V)     | Power    |
| GND          | Pin 6 (GND)      | Ground   |
| TX           | Pin 10 (RXD)     | UART TX  |
| RX           | Pin 8 (TXD)      | UART RX  |
| RST          | Pin 12 (GPIO 18) | Reset    |

Optional: For more secure mounting, use the 3D-printed case designs in the `3dprint` directory.

**Success Criteria**: When properly connected, the ESP32-H2 power LED should light up, and the board should be visible when you run `ls /dev/ttyUSB*` or `ls /dev/ttyACM*` on the Raspberry Pi.

### 2. Raspberry Pi OS Installation

1. Download the [Raspberry Pi Imager](https://www.raspberrypi.org/software/) for your platform
2. Insert the microSD card into your computer
3. Launch Raspberry Pi Imager
4. Select Raspberry Pi OS (64-bit) Bookworm
5. Configure SSH, Wi-Fi, and other settings before writing
6. Write the OS to the SD card
7. Insert the SD card into your Raspberry Pi and power it on

**Success Criteria**: Your Raspberry Pi should boot to the login prompt or desktop. If you configured SSH, you should be able to connect with `ssh pi@raspberrypi.local` or using the IP address you configured.

## Software Setup

### 1. Initial System Configuration

Connect to your Raspberry Pi via SSH:

```bash
ssh pi@raspberrypi.local  # Use the hostname or IP address you configured
```

Update the system:

```bash
sudo apt update && sudo apt upgrade -y
```

**Success Criteria**: The command should complete without errors, and you'll see packages being updated and installed.

### 2. Install Dependencies

Run the integrated setup task to install required dependencies:

```bash
sudo task setup-raspberry-pi
```

This comprehensive setup task will:
1. Install all required packages (Docker, Docker Compose, Python, etc.)
2. Configure Docker service and add your user to the Docker group
3. Install the Task runner
4. Set up the firewall with appropriate rules
5. Configure system settings for Thread networking

For a more minimal setup with just the basic dependencies:

```bash
task install-deps-raspberry
```

**Success Criteria**: Verify the installation with the following commands:
```bash
docker --version      # Should show Docker version
task --version        # Should show Task version
python3 --version     # Should show Python version
```

### 3. Clone the Repository

```bash
git clone https://github.com/yourusername/diythreadrouter.git
cd diythreadrouter
```

**Success Criteria**: The repository should be cloned successfully, and you should see the project files when you run `ls`.

### 4. Configure the Stack

Review the configuration files in the `configs/` directory:

- `otbr/otbr-web.conf` - Web interface configuration
- `prometheus/prometheus.yml` - Prometheus configuration
- `grafana/provisioning/` - Grafana provisioning

For most users, the default configurations will work out of the box.

## Next Steps

- [Flash the ESP32-H2 firmware](./firmware.md)
- [Configure the Thread Border Router](./configuration.md)
- [Start the services and create your Thread network](./operation.md)
