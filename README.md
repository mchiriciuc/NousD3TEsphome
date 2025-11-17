# NOUS D3T 25A Smart Switch - ESPHome Configuration

[![Made for ESPHome](https://img.shields.io/badge/Made%20for-ESPHome-black?logo=esphome)](https://esphome.io)

Professional ESPHome firmware for NOUS D3T 25A Smart Switch with BL0942 power monitoring, complete with safety protections and calibrated energy metering.

## Features

- ✅ **Accurate Power Monitoring**: BL0942 chip with calibrated voltage and current measurements
- 🔥 **Temperature Protection**: Automatic shutdown on overheating
- ⚡ **Overvoltage/Undervoltage Protection**: Configurable thresholds
- 🔌 **Overcurrent Protection**: Prevents damage from excessive current
- 📊 **Comprehensive Energy Metrics**: Power, apparent power, reactive power, and power factor
- 🎛️ **Web Interface**: Built-in configuration and monitoring
- 🏠 **Home Assistant Ready**: Full integration with auto-discovery
- 💾 **State Memory**: Optional remember last state on power loss

## Quick Start with ESPHome Dashboard

### Method 1: Direct Import (Recommended)

1. Open your ESPHome Dashboard
2. Click "New Device" → "Continue" → "Skip"
3. Give your device a name (e.g., "kitchen-switch")
4. In the device configuration, replace the content with:

```yaml
substitutions:
  device_name: "kitchen-switch"
  friendly_name: "Kitchen Switch"
  
  # IMPORTANT: Calibrate these values for your specific device!
  voltage_calibration: "16024.5" 
  current_calibration: "127074.8"

packages:
  nous_d3t: github://mchiriciuc/NousD3TEsphome/firmware.yaml@main

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password
```

5. Add your WiFi credentials to `secrets.yaml`:
```yaml
wifi_ssid: "YourWiFiName"
wifi_password: "YourWiFiPassword"
```

6. Click "Install" and choose your installation method

### Method 2: Clone and Customize

1. Clone this repository:
```bash
git clone https://github.com/mchiriciuc/NousD3TEsphome.git
cd NousD3TEsphome
```

2. Create your device configuration (e.g., `my-switch.yaml`):
```yaml
substitutions:
  device_name: "my-switch"
  friendly_name: "My Switch"
  
  # Your calibrated values here
  voltage_calibration: "16024.5" 
  current_calibration: "127074.8"

packages:
  nous_d3t: !include firmware.yaml

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password
```

3. Install:
```bash
esphome run my-switch.yaml
```

## Calibration Guide

For accurate power measurements, you need to calibrate the BL0942 chip for your specific device:

### Voltage Calibration

1. Measure actual voltage with a known-accurate multimeter
2. Compare with ESPHome reading
3. Adjust `voltage_calibration` using formula:
   ```
   new_calibration = current_calibration × (actual_voltage / esphome_reading)
   ```

### Current Calibration

1. Use a known resistive load (e.g., incandescent bulb)
2. Measure actual current with clamp meter
3. Adjust `current_calibration` using formula:
   ```
   new_calibration = current_calibration × (actual_current / esphome_reading)
   ```

### Example Calibration Values

Different batches may need different values. Here are some examples:

| Batch/Version | Voltage Cal | Current Cal | Notes |
|---------------|-------------|-------------|-------|
| Default       | 16024.5     | 127074.8    | Factory default |
| Batch 2023-A  | 15980.2     | 126500.0    | Slightly higher readings |
| Batch 2023-B  | 16100.8     | 128000.5    | Slightly lower readings |

## Multiple Devices

You can manage multiple devices easily:

```yaml
# Device 1: kitchen-switch.yaml
substitutions:
  device_name: "kitchen-switch"
  friendly_name: "Kitchen Switch"
  voltage_calibration: "16024.5"
  current_calibration: "127074.8"

packages:
  nous_d3t: github://mchiriciuc/NousD3TEsphome/firmware.yaml@main

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password
```

```yaml
# Device 2: garage-switch.yaml
substitutions:
  device_name: "garage-switch"
  friendly_name: "Garage Switch"
  voltage_calibration: "15980.2"  # Different calibration
  current_calibration: "126500.0"

packages:
  nous_d3t: github://mchiriciuc/NousD3TEsphome/firmware.yaml@main

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password
```

## Safety Features

### Configurable Thresholds

All protection thresholds can be adjusted from Home Assistant:

- **Max Temperature**: 30-80°C (default: 45°C)
- **Max Current**: 1-30A (default: 25A)
- **Max Voltage**: 240-270V (default: 253V)
- **Min Voltage**: 150-200V (default: 150V)

### Protection Actions

When any threshold is exceeded:
1. Relay automatically turns OFF
2. Event sent to Home Assistant
3. Notification created (if configured)
4. Detailed log entry created

The relay will remain locked until the condition clears and you manually reset it.

## Hardware Specifications

- **Chip**: ESP32 (ESP32-WROOM-32)
- **Power Meter**: BL0942
- **Relay**: Latching (bistable)
- **Max Load**: 25A @ 230V AC
- **Temperature Sensor**: NTC 10kΩ
- **WiFi**: 2.4GHz 802.11 b/g/n

## Pin Configuration

| Function | GPIO |
|----------|------|
| UART TX  | 14   |
| UART RX  | 13   |
| Relay ON | 2    |
| Relay OFF| 12   |
| Button   | 0    |
| Red LED  | 5    |
| Status LED| 15  |
| Temperature| 34 |

## Available Entities

### Sensors
- Voltage (V)
- Current (A)
- Power (W)
- Apparent Power (VA)
- Reactive Power (VAR)
- Power Factor
- Frequency (Hz)
- Temperature (°C)
- WiFi Signal (dB)

### Controls
- Main Switch (ON/OFF)
- Remember Last State (toggle)
- Protection Thresholds (4 configurable numbers)
- Factory Reset button

### Diagnostics
- Uptime
- IP Address
- SSID
- MAC Address
- ESPHome Version

## Troubleshooting

### Power readings are incorrect
- Calibrate the device using the guide above
- Check that UART is not being used by logger (baud_rate: 0)
- Verify connections and power supply

### Device won't turn on
- Check temperature - may be in thermal protection
- Check voltage/current thresholds
- Review logs for protection triggers

### WiFi connection issues
- Device will create a hotspot "NOUS D3T Hotspot" / password: "setup1234"
- Connect and configure WiFi through captive portal

## Project Background

This configuration was developed to resolve accuracy issues with the original ESPHome BL0942 component. Through detailed analysis comparing ESPHome and Tasmota implementations, we discovered that the BL0942 works best with default register settings rather than attempting runtime configuration.

Key improvements:
- Removed unnecessary register configuration attempts
- Simplified component to match proven Tasmota approach
- Added comprehensive safety protections
- Calibrated for accurate measurements

## Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Credits

- Original hardware: NOUS D3T 25A Switch
- Power monitoring: BL0942 chip
- Firmware: ESPHome
- Developed by: [Marius Chiriciuc](https://github.com/mchiriciuc)

## Support

For issues, questions, or contributions:
- GitHub Issues: https://github.com/mchiriciuc/NousD3TEsphome/issues
- ESPHome Community: https://community.home-assistant.io/c/esphome/

---

**⚠️ Safety Notice**: This device handles mains voltage (230V AC). Installation should only be performed by qualified electricians. Always disconnect power before installation or maintenance.
