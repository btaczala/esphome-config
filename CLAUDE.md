# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an ESPHome configuration repository for managing multiple ESP32-based IoT devices connected to Home Assistant. ESPHome is a framework that allows you to configure and flash ESP microcontrollers using YAML configuration files.

## Device Configurations

The repository contains configurations for the following devices:

- **m5dial.yaml**: M5Stack Dial with rotary encoder, touchscreen, NFC reader, and LVGL interface for controlling a desk height
- **esp32-s3.yaml**: Waveshare ESP32-S3 with 800x480 RGB display, LVGL interface, and antiburn features
- **office-dashboard.yaml**: Waveshare ESP32-S3 4.3" touch display used as an office dashboard
- **nspanel-sypialnia.yaml**: NSPanel in bedroom, using external package from Blackymas/NSPanel_HA_Blueprint
- **nspanel-garaz.yaml**: NSPanel in garage, using external package from Blackymas/NSPanel_HA_Blueprint
- **secrets.yaml**: Contains WiFi credentials and other sensitive data (excluded from version control)

## Common Commands

### Validate Configuration
```bash
esphome config <device>.yaml
```

### Compile Firmware
```bash
esphome compile <device>.yaml
```

### Upload Firmware
For OTA (Over-The-Air) updates to devices already running ESPHome:
```bash
esphome upload <device>.yaml
```

For initial USB flash or troubleshooting:
```bash
esphome upload <device>.yaml --device /dev/ttyUSB0
```

### View Logs
```bash
esphome logs <device>.yaml
```

### Full Workflow (Compile + Upload + Logs)
```bash
esphome run <device>.yaml
```

### Clean Build Files
For a specific device:
```bash
esphome clean <device>.yaml
```

For all devices:
```bash
esphome clean-all
```

### Dashboard
Start the web interface for managing devices:
```bash
esphome dashboard .
```

## Architecture

### Device Types

1. **M5Stack Dial (m5dial.yaml)**
   - ESP32-S3 with ESP-IDF framework
   - Hardware: Rotary encoder, RC522 NFC reader, ILI9XXX display (GC9A01A), FT5x06 touchscreen, PCF8563 RTC
   - Encoder behavior: By default rotates through pages; when button is clicked on a page, encoder controls Megadesk height
   - Integration: Communicates with Home Assistant to control Megadesk height
   - Display: LVGL-based UI showing desk height

2. **Waveshare ESP32-S3 (esp32-s3.yaml)**
   - ESP32-S3-DevKitC-1 with ESP-IDF framework
   - Hardware: Large 800x480 RGB display (RPI DPI), GT911 touchscreen, CH422G I/O expander
   - Features: PSRAM (octal mode), backlight control, antiburn mode (runs at specific times to prevent OLED burn-in)
   - Display: LVGL-based dashboard with configurable idle timeout

3. **Office Dashboard (office-dashboard.yaml)**
   - ESP32-S3-DevKitC-1 with ESP-IDF framework
   - Hardware: Waveshare 4.3" 800x480 MIPI RGB display, GT911 touchscreen, CH422G I/O expander
   - Features: PSRAM (octal mode), backlight control via LEDC PWM, screen on/off switch
   - Display: LVGL-based dashboard; touching screen wakes it if off; boot screen hidden on HA API connect

4. **NSPanels (nspanel-*.yaml)**
   - Use remote package system from Blackymas/NSPanel_HA_Blueprint
   - Configuration is primarily through substitutions and customization sections
   - Both panels use manual IP addressing

### Key Patterns

- **Substitutions**: Used for device-specific values (device name, IPs, URLs)
- **Secrets**: WiFi credentials and passwords stored in `secrets.yaml` and referenced with `!secret`
- **Packages**: NSPanel configs use external packages from GitHub, auto-refreshed every 300s
- **Custom Components**: Some configs may use components from `.esphome/external_components/`
- **LVGL**: Used for graphical interfaces on devices with displays
- **Home Assistant Integration**: Devices communicate via the `api:` component and can call HA services or subscribe to entity states

### Build System

- ESPHome uses PlatformIO under the hood for compilation
- Build artifacts are stored in `.esphome/build/<device-name>/`
- Device metadata is stored in `.esphome/storage/<device>.yaml.json`
- External components and packages are cached in `.esphome/external_components/` and `.esphome/packages/`

### Framework Types

This repository uses two ESP-IDF framework configurations:
- Standard ESP-IDF (M5Stack Dial)
- ESP-IDF with custom sdkconfig options for performance (Waveshare ESP32-S3)

## Development Workflow

1. Modify the appropriate `.yaml` file for your device
2. Validate the configuration with `esphome config <device>.yaml`
3. If adding new features, compile first to catch errors: `esphome compile <device>.yaml`
4. Upload to the device: `esphome upload <device>.yaml`
5. Monitor logs: `esphome logs <device>.yaml`

## Important Notes

- The `.esphome/` directory contains build artifacts and should not be committed to version control
- `secrets.yaml` contains sensitive information and should be excluded from version control
- When modifying NSPanel configs, be aware that base configuration comes from the external package
- LVGL configurations require understanding of the LVGL widget system
- Home Assistant entity IDs referenced in configs must exist in your HA instance
