# ESP32-H2 Firmware Guide

This guide provides information about the ESP32-H2 firmware used as the Radio Co-Processor (RCP) in this project, how to flash it, and how to customize it if needed.

## About the Firmware

The ESP32-H2 acts as a Radio Co-Processor (RCP) in this project, handling the Thread radio functions while the Raspberry Pi handles the higher-level networking and application functions. There are a few examples available in the espressif repo, of which only ONE is correct: https://github.com/espressif/esp-idf/tree/master/examples/openthread/ot_rcp

### Firmware Features

The default firmware includes:

- Full Thread 1.3 protocol support
- IEEE 802.15.4 radio driver for ESP32-H2
- Spinel protocol implementation for host communication
- HDLC framing for UART communication

## Flashing the Firmware

### Direct Flashing

For fast flashing we have a task

```bash
task flashPrefabFirmware
```
This downloads the esptool binary and uses the firmware that is preloaded in this repo. The task is setup to work on a Raspberry Pi, is lightweight and fast to execute. If you dont care about customizing your firmware, this is the preferred option. 

## Customizing the Firmware

If you need to customize the firmware, you'll need to build it from source.

### Building from Source

For building from source you can follow the three steps that have been made available in the Taskfile. The process involves:
* Pulling the repo and getting all the code
* Configuring and building the binaries
* Flashing the device

```bash
task buildFirmware # for the total rundown
task _configure_idfpy # to pull all software 
task _build_firmware # to configure and build firmware
task _flash_firmware_from_build # to flash the firmware.
```

These steps give you control to manipulate the settings of the build. 

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
