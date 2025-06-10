# Troubleshooting Guide
# Troubleshooting Guide

This guide helps you troubleshoot common issues with your DIY Thread Border Router.

## ESP32-H2 Flashing Issues

### Cannot Connect to ESP32-H2

**Symptoms**: `task flash-esp32h2` fails with connection errors.

**Solutions**:

1. Check physical connections between Raspberry Pi and ESP32-H2
2. Verify the correct port is being used (`/dev/ttyUSB0` or `/dev/ttyACM0`)
3. Ensure you have permissions to access the serial port:
   ```bash
   sudo usermod -a -G dialout $USER
   # Log out and log back in for changes to take effect
   ```
4. Try putting the ESP32-H2 into download mode manually:
   - Hold the BOOT button
   - Press and release the RST button
   - Release the BOOT button

### Firmware Flash Fails

**Symptoms**: `task flash-esp32h2` starts but fails during flashing.

**Solutions**:

1. Try a different USB cable (some cables are charge-only)
2. Update esptool.py: `pip install --upgrade esptool`
3. Try a slower baud rate:
   ```bash
   python3 -m esptool --chip esp32h2 --port /dev/ttyUSB0 --baud 115200 \
     write_flash 0x0 ./firmware/binaries/ot-rcp-esp32h2.bin
   ```

## Docker Issues

### Services Fail to Start

**Symptoms**: `task start` fails or some containers are not running.

**Solutions**:

1. Check logs for specific errors: `docker-compose logs`
2. Ensure Docker is running: `sudo systemctl status docker`
3. Check if there are port conflicts:
   ```bash
   sudo lsof -i :80
   sudo lsof -i :3000
   ```
4. Try pulling the images manually:
   ```bash
   docker-compose pull
   ```
5. Restart Docker: `sudo systemctl restart docker`

### Container Keeps Restarting

**Symptoms**: `docker-compose ps` shows a container constantly restarting.

**Solutions**:

1. Check specific container logs: `docker-compose logs <service-name>`
2. Verify configuration files for that service
3. Check system resources: `htop` (install with `sudo apt install htop` if needed)
4. Increase Docker logging for detailed output:
   ```bash
   docker-compose logs --tail=100 <service-name>
   ```

## Thread Network Issues

### Cannot Form Thread Network

**Symptoms**: "Form" button in the web interface fails to create a network.

**Solutions**:

1. Check OTBR logs: `docker-compose logs otbr`
2. Verify ESP32-H2 connection:
   ```bash
   docker-compose exec otbr ot-ctl
   > state
   ```
   Should show "disabled" (ready to form a network)
3. Reset the OTBR container: `docker-compose restart otbr`
4. Check that the ESP32-H2 firmware is correctly flashed

### Devices Cannot Join Thread Network

**Symptoms**: Thread devices cannot connect to your network.

**Solutions**:

1. Verify network is operating: `docker-compose exec otbr ot-ctl state`
2. Check if the network is at capacity (too many router devices)
3. Ensure devices are within radio range
4. Verify commissioning credentials are correct
5. Try resetting the device to factory defaults

## Web Interface Issues

### Cannot Access Web Interface

**Symptoms**: Unable to access the OTBR web interface at `http://<raspberry-pi-ip>:80`.

**Solutions**:

1. Verify OTBR container is running: `docker-compose ps otbr`
2. Check if another service is using port 80:
   ```bash
   sudo lsof -i :80
   ```
3. Verify network connectivity between your computer and Raspberry Pi
4. Check OTBR logs for errors: `docker-compose logs otbr`
5. Try restarting the OTBR service: `docker-compose restart otbr`

## Monitoring Issues

### Grafana Shows No Data

**Symptoms**: Grafana dashboards show "No data" or query errors.

**Solutions**:

### Reset Pin Connection

The RST (reset) pin connection mentioned in earlier versions of the documentation is not required for normal operation:

- Modern ESP32-H2 boards handle automatic reset during flashing via the USB connection
- The esptool.py utility can typically manage the reset sequence without a dedicated GPIO connection

If you're experiencing issues with automatic reset during flashing:

1. Try using the physical reset button on the ESP32-H2 board (if available) right as the flashing begins
2. Try a different USB cable or port
3. Only as a last resort, connect the RST pin to GPIO 18 (Pin 12) on the Raspberry Pi

1. Check Prometheus is running: `docker-compose ps prometheus`
2. Verify Prometheus can scrape targets:
   ```
   http://<raspberry-pi-ip>:9090/targets
   ```
3. Check Prometheus logs: `docker-compose logs prometheus`
4. Verify OTBR metrics endpoint is accessible:
   ```bash
   curl http://localhost:8080/metrics
   ```
5. Restart the monitoring stack:
   ```bash
   docker-compose restart prometheus grafana
   ```

## System Issues

### Raspberry Pi Performance Problems

**Symptoms**: System is slow, unresponsive, or services crash randomly.

**Solutions**:

1. Check system resources: `htop`
2. Monitor temperature: `vcgencmd measure_temp`
3. Ensure adequate cooling for your Raspberry Pi
4. Check SD card health:
   ```bash
   sudo apt install smartmontools
   sudo smartctl -a /dev/mmcblk0
   ```
5. Consider using a higher quality power supply (3A+ recommended)

### Network Connectivity Issues

**Symptoms**: Intermittent connection to the Raspberry Pi.

**Solutions**:

1. Use Ethernet instead of Wi-Fi if possible
2. If using Wi-Fi, check signal strength: `iwconfig wlan0`
3. Consider setting a static IP address for the Raspberry Pi
4. Restart networking: `sudo systemctl restart networking`

## Getting Help

If you're still experiencing issues:

1. Collect logs for all services: `docker-compose logs > all-logs.txt`
2. Check system logs: `sudo journalctl -xe > system-logs.txt`
3. Create a detailed description of your issue including:
   - Hardware setup details
   - Steps to reproduce the problem
   - Error messages and logs

## Next Steps

- [Setup Guide](./setup.md) - Revisit setup instructions
- [Configuration Guide](./configuration.md) - Check configuration settings
- [Operation Guide](./operation.md) - Review operating procedures
This guide helps you troubleshoot common issues with your DIY Thread Border Router.

## ESP32-H2 Flashing Issues

### Cannot Connect to ESP32-H2

**Symptoms**: `task flash-esp32h2` fails with connection errors.

**Solutions**:
1. Check physical connections:
   ```bash
   ls -l /dev/ttyUSB*
   ```
2. Ensure you have the correct permissions:
   ```bash
   sudo usermod -a -G dialout $USER
   # Log out and log back in for changes to take effect
   ```
3. Try entering bootloader mode manually:
   - Hold down the BOOT button on the ESP32-H2
   - Press and release the RESET button
   - Release the BOOT button

### Wrong Firmware or Flashing Errors

**Symptoms**: Flashing completes but the device doesn't work correctly.

**Solutions**:
1. Check you're using the correct firmware version:
   ```bash
   task download-esp32h2-firmware
   ```
2. Try a slower baud rate in `Taskfile.yml`:
   - Change `--baud 460800` to `--baud 115200`

## Docker Issues

### Container Won't Start

**Symptoms**: `task start` fails to start containers.

**Solutions**:
1. Check Docker service is running:
   ```bash
   sudo systemctl status docker
   ```
2. Verify Docker Compose installation:
   ```bash
   docker-compose --version
   ```
3. Check logs for specific errors:
   ```bash
   docker-compose logs
   ```

### Device Access Issues

**Symptoms**: OTBR container can't access the ESP32-H2 device.

**Solutions**:
1. Check the device path in `docker-compose.yml` matches your actual device:
   ```bash
   ls -l /dev/ttyUSB*
   ```
2. Make sure the device is properly mapped in the container:
   ```bash
   docker exec -it otbr ls -l /dev/ttyUSB0
   ```

## Thread Network Issues

### Cannot Form or Join Network

**Symptoms**: The "Form" or "Join" actions fail in the web interface.

**Solutions**:
1. Check if the RCP is operational:
   ```bash
   task network-scan
   ```
2. Restart the entire stack:
   ```bash
   task restart
   ```
3. Check OTBR logs for specific errors:
   ```bash
   docker-compose logs otbr
   ```

### Cannot Connect Thread Devices

**Symptoms**: Thread devices can't join the network.

**Solutions**:
1. Verify the Thread network is up:
   ```bash
   task network-info
   ```
2. Check that NAT64 is enabled (for IPv6-to-IPv4 translation):
   ```bash
   docker-compose exec otbr ot-ctl nat64
   ```
3. Make sure you're using the correct network credentials when joining devices.

## Web Interface Issues

**Symptoms**: Can't access the web interface or dashboards.

**Solutions**:
1. Check that all containers are running:
   ```bash
   task status
   ```
2. Verify network connectivity to the Raspberry Pi:
   ```bash
   ping <raspberry-pi-ip>
   ```
3. Check port availability:
   ```bash
   sudo netstat -tulpn | grep -E '80|3000|8080|8081|9090'
   ```

## Getting Additional Help

If you're still experiencing issues:

1. Collect diagnostic information:
   ```bash
   docker-compose logs > logs.txt
   task network-info > network-info.txt
   lsusb > usb-devices.txt
   ```

2. Open an issue on the GitHub repository with these files attached.

3. For more advanced debugging, you can access the OTBR shell directly:
   ```bash
   docker-compose exec otbr bash
   ```
