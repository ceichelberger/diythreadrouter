# ESP32-H2 Firmware Guide

This guide provides information about the ESP32-H2 firmware used as the Radio Co-Processor (RCP) in this project, how to flash it, and how to customize it if needed.

## About the Firmware

The ESP32-H2 acts as a Radio Co-Processor (RCP) in this project, handling the Thread radio functions while the Raspberry Pi handles the higher-level networking and application functions.

### Pre-built Firmware

For convenience, this project uses pre-built OpenThread RCP firmware from Espressif, which is downloaded automatically by the `task download-esp32h2-firmware` command.

### Firmware Features

The default firmware includes:

- Full Thread 1.3 protocol support
- IEEE 802.15.4 radio driver for ESP32-H2
- Spinel protocol implementation for host communication
- HDLC framing for UART communication

## Flashing the Firmware

### Automatic Flashing (Recommended)

The easiest way to flash the firmware is using the provided Taskfile command:

```bash
task flash-esp32h2
```

This will:
1. Download the pre-built firmware if not already downloaded
2. Install esptool.py if needed
3. Flash the firmware to the ESP32-H2

**Note**: The ESP32-H2 board should be connected via USB for flashing. Modern boards support automatic reset during the flashing process, so a direct reset pin connection to the Raspberry Pi is not required.

**Success Criteria**: The flashing process should complete without errors, showing progress and a successful flash message. The ESP32-H2 will restart automatically after flashing.

### Manual Flashing

If you need more control over the flashing process:

```bash
# Install esptool.py if not already installed
python3 -m pip install esptool

# Flash the firmware
python3 -m esptool --chip esp32h2 --port /dev/ttyUSB0 --baud 460800 \
  write_flash 0x0 ./firmware/binaries/ot-rcp-esp32h2.bin
```

Replace `/dev/ttyUSB0` with the actual port where your ESP32-H2 is connected.

## Customizing the Firmware

If you need to customize the firmware, you'll need to build it from source.

### Building from Source

1. **Clone the Espressif OpenThread repository**:

   ```bash
   git clone --recursive https://github.com/espressif/esp-idf.git
   cd esp-idf
   git checkout release/v5.1  # or the latest stable version
   ./install.sh
   . ./export.sh
   ```

2. **Navigate to the OpenThread RCP example**:

   ```bash
   cd examples/openthread/ot_rcp
   ```

3. **Configure the firmware** (optional):

   ```bash
   idf.py menuconfig
   ```

   In the configuration menu, you can adjust various settings under "Component config" > "OpenThread".

4. **Build the firmware**:

   ```bash
   idf.py build
   ```

5. **Flash the custom firmware**:

   ```bash
   idf.py -p /dev/ttyUSB0 flash
   ```

## Firmware Customization Options

Common customization options include:

### Radio Channel Selection

The Thread radio channel can be configured (default is dynamic channel selection). You can select a specific channel to use:

- Component config → OpenThread → Channel → Custom channel → Enter channel (11-26)

### Transmit Power

Adjust the radio transmit power to increase range or reduce power consumption:

- Component config → OpenThread → TX Power

### UART Configuration

If you're using a different UART setup:

- Component config → OpenThread → UART settings

## Firmware Source Code

The source code for the OpenThread RCP firmware is available from:

- **Espressif OpenThread fork**: [https://github.com/espressif/esp-idf](https://github.com/espressif/esp-idf) (see the examples/openthread/ot_rcp directory)
- **Main OpenThread repository**: [https://github.com/openthread/openthread](https://github.com/openthread/openthread)

## Troubleshooting

If you encounter issues with flashing or the firmware operation, see the [Troubleshooting Guide](./troubleshooting.md#esp32-h2-issues).

## Next Steps

- [Configuration Guide](./configuration.md)
- [Operation Guide](./operation.md)
