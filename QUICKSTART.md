# Quick Start Template

Copy this template and customize for your device!

## Step 1: Copy this configuration

```yaml
# Give your device a unique name
substitutions:
  device_name: "my-nous-switch"
  friendly_name: "My NOUS Switch"
  
  # IMPORTANT: Calibrate these values for accurate measurements!
  # See CALIBRATION.md for detailed instructions
  # Default values shown - should be calibrated per device
  voltage_calibration: "16024.5"
  current_calibration: "127074.8"
  
  # Optional: Adjust protection thresholds (can also change in Home Assistant)
  # default_max_temp: "45.0"
  # default_max_current: "25.0"
  # default_max_voltage: "253.0"
  # default_min_voltage: "150.0"

# Import base configuration from GitHub
packages:
  nous_d3t: github://mchiriciuc/NousD3TEsphome/firmware.yaml@main

# WiFi configuration - REQUIRED
wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password

# That's it! The base configuration includes everything else.
# Flash to your device and you're ready to go!
```

## Step 2: Create secrets.yaml

In your ESPHome directory, create or edit `secrets.yaml`:

```yaml
wifi_ssid: "YourWiFiName"
wifi_password: "YourWiFiPassword"
```

## Step 3: Flash your device

### First-time flash (USB):
```bash
esphome run my-nous-switch.yaml
```

### Subsequent updates (OTA):
Just click "Install" → "Wirelessly" in ESPHome dashboard

## What You Get

After flashing, your device will have:

### Sensors (automatically in Home Assistant)
- ⚡ Voltage, Current, Power
- 📊 Apparent Power, Reactive Power, Power Factor  
- 📡 Frequency
- 🌡️ Temperature
- 📶 WiFi Signal

### Controls
- 🔌 Main Switch (ON/OFF)
- 💾 Remember Last State toggle
- 🛡️ Configurable protection thresholds

### Protections
- 🔥 Automatic shutdown on overheating
- ⚡ Overvoltage/Undervoltage protection
- 🔌 Overcurrent protection

## Next Steps

1. ✅ **Flash the configuration**
2. ✅ **Verify device shows up in Home Assistant**
3. ✅ **Test the switch turns on/off**
4. ⚠️ **Calibrate for accurate measurements** (see CALIBRATION.md)
5. 🎉 **Enjoy your smart switch!**

## Customization Examples

### Change device name after initial setup
```yaml
substitutions:
  device_name: "new-name"  # Change this
  friendly_name: "New Name"  # And this
  # ... rest stays the same
```

### Use static IP address
```yaml
wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password
  manual_ip:
    static_ip: 192.168.1.100
    gateway: 192.168.1.1
    subnet: 255.255.255.0
```

### Disable web server (more secure)
```yaml
packages:
  nous_d3t: github://mchiriciuc/NousD3TEsphome/firmware.yaml@main

web_server: !remove  # Disables the web interface
```

### Change update interval
```yaml
packages:
  nous_d3t: github://mchiriciuc/NousD3TEsphome/firmware.yaml@main

sensor:
  - platform: bl0942
    update_interval: 5s  # Change from default 1s
```

## Common Issues

### "Failed to connect to WiFi"
- Check WiFi credentials in secrets.yaml
- Device will create hotspot "NOUS D3T Hotspot" / password: "setup1234"
- Connect to hotspot and configure WiFi

### "Device not showing in Home Assistant"
- Check device is powered on
- Verify WiFi connection (check device logs)
- Check Home Assistant ESPHome integration is enabled

### "Power readings seem wrong"
- You need to calibrate! See CALIBRATION.md
- Default values are approximate, each device needs calibration

## Need Help?

- 📖 Full documentation: [README.md](README.md)
- 🔧 Calibration guide: [CALIBRATION.md](CALIBRATION.md)
- 📝 Examples: [examples/](examples/)
- 🐛 Report issues: [GitHub Issues](https://github.com/mchiriciuc/NousD3TEsphome/issues)

---

**Safety Warning**: This device controls mains voltage (230V AC). Only install if you're qualified to work with electrical installations. Improper installation can cause electric shock, fire, or death.
