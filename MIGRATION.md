# Migration Guide - v2.0.0

## What's New in v2.0.0

Version 2.0.0 introduces **dashboard_import** support, making it easier to manage multiple NOUS D3T devices and keep them updated.

### Key Changes

✅ **Dashboard Import Support**
- Devices can now be configured using the ESPHome dashboard import feature
- Base configuration hosted on GitHub for easy updates
- Simplifies multi-device management

✅ **Package-Based Architecture**
- Main configuration split into importable package
- Device-specific settings via substitutions
- Cleaner, more maintainable structure

✅ **"Made for ESPHome" Compliant**
- Follows ESPHome best practices
- Proper project metadata
- Ready for community adoption

✅ **Improved Documentation**
- Comprehensive README
- Detailed calibration guide
- Multiple configuration examples

### Breaking Changes

⚠️ **Configuration Structure Changed**

**Old way (v1.x):**
```yaml
# Everything in one file
esphome:
  name: "nous-d3t"
  # ... hundreds of lines ...
```

**New way (v2.0):**
```yaml
# Minimal device config
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

⚠️ **WiFi Configuration Removed from Base**

WiFi credentials are no longer in the base `firmware.yaml`. You must add them to your device-specific configuration.

**Required addition:**
```yaml
wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password
```

⚠️ **Project Name Changed**

- Old: `NOUS.D3T` / `1.5.1`
- New: `nous.d3t-25a` / `2.0.0`

This follows ESPHome naming conventions and won't affect functionality.

## Migration Steps

### Option 1: Start Fresh (Recommended)

1. **Backup your old config** (just in case)

2. **Create new device config** using the v2.0 structure:

```yaml
substitutions:
  device_name: "my-switch"
  friendly_name: "My Switch"
  
  # Use your existing calibration values!
  voltage_calibration: "YOUR_OLD_VALUE"
  current_calibration: "YOUR_OLD_VALUE"
  
  # Optional: Use your old protection thresholds
  default_max_temp: "45.0"
  default_max_current: "25.0"
  default_max_voltage: "253.0"
  default_min_voltage: "150.0"

packages:
  nous_d3t: github://mchiriciuc/NousD3TEsphome/firmware.yaml@main

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password
```

3. **Flash OTA** or via USB - your device will update seamlessly

4. **Verify** all sensors and controls work

### Option 2: Gradual Migration

If you have many devices and want to migrate gradually:

1. **Keep v1.x devices running** - they'll continue to work

2. **New devices** use v2.0 structure

3. **Migrate existing devices** when convenient (OTA works fine)

## What's Preserved

✅ **All functionality** - nothing removed, only reorganized
✅ **Calibration values** - copy from your old config
✅ **Protection thresholds** - can be set via substitutions or Home Assistant
✅ **Device state** - preserved through OTA update
✅ **Home Assistant entities** - same names, no reconfiguration needed

## Multiple Device Management

### Before (v1.x)

Each device had a complete copy of the entire configuration:
```
kitchen-switch.yaml (300+ lines)
garage-switch.yaml (300+ lines)  
office-switch.yaml (300+ lines)
```

Any bug fix meant updating all files!

### After (v2.0)

Each device has minimal config, imports from GitHub:
```
kitchen-switch.yaml (15 lines)
garage-switch.yaml (15 lines)
office-switch.yaml (15 lines)
```

Bug fixes in firmware.yaml automatically available to all devices on next flash!

## Getting Updates

### Automatic Updates

When you use:
```yaml
packages:
  nous_d3t: github://mchiriciuc/NousD3TEsphome/firmware.yaml@main
```

ESPHome will check for updates when you compile. To get updates:

1. **Open ESPHome dashboard**
2. **Click "Install"** on your device
3. **Compile and flash** - new version automatically pulled

### Pinning to Specific Version

For production stability, pin to a specific version:
```yaml
packages:
  nous_d3t: github://mchiriciuc/NousD3TEsphome/firmware.yaml@v2.0.0
```

Change version tag when you're ready to update.

### Local Development

For testing or customization:
```yaml
packages:
  nous_d3t: !include /config/esphome/nous-d3t/firmware.yaml
```

Clone the repo locally and point to your copy.

## Calibration Values

⚠️ **IMPORTANT**: Copy your calibration values from v1.x config!

In your old config, find:
```yaml
substitutions:
  voltage_calibration: "16024.5"  # ← Copy this
  current_calibration: "127074.8"  # ← Copy this
```

Use these same values in your v2.0 config. These are device-specific and critical for accuracy!

## Example Migration

### Your old v1.x config (kitchen-switch.yaml):

```yaml
substitutions:
  device_name: "kitchen-switch"
  friendly_name: "Kitchen Switch"
  voltage_calibration: "15980.2"  # Your calibrated value
  current_calibration: "126500.0"  # Your calibrated value
  # ... more settings ...

esphome:
  name: "${device_name}"
  # ... 300+ lines of config ...

wifi:
  ssid: "MyWiFi"
  password: "secret123"
  
# ... everything else ...
```

### Your new v2.0 config (kitchen-switch.yaml):

```yaml
substitutions:
  device_name: "kitchen-switch"
  friendly_name: "Kitchen Switch"
  voltage_calibration: "15980.2"  # SAME value
  current_calibration: "126500.0"  # SAME value

packages:
  nous_d3t: github://mchiriciuc/NousD3TEsphome/firmware.yaml@main

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password
```

That's it! 300+ lines reduced to ~15 lines.

## Troubleshooting Migration

### "Failed to fetch package from GitHub"
- Check internet connectivity
- Try pinning to specific version: `@v2.0.0` instead of `@main`
- Use local copy as fallback: `!include firmware.yaml`

### "WiFi not configured"
Add to your device config:
```yaml
wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password
```

### "Calibration seems off after migration"
- Verify you copied calibration values correctly
- Check no extra spaces or quotes
- Re-calibrate if needed (see CALIBRATION.md)

### "Home Assistant entities duplicated"
- Old and new have same entity names - this is expected
- After confirming new device works, delete old device from HA
- Or change `device_name` to create new entities

## Need Help?

- **GitHub Issues**: https://github.com/mchiriciuc/NousD3TEsphome/issues
- **ESPHome Discord**: https://discord.gg/A7SaJfY
- **Home Assistant Community**: https://community.home-assistant.io/c/esphome/

## Rollback

If you need to rollback to v1.x:

1. Flash your old v1.x config via OTA or USB
2. Or use pinned version: `github://mchiriciuc/NousD3TEsphome/firmware.yaml@v1.5.1`
3. No data loss - all settings preserved

---

**Remember**: v2.0 is not a functional change - it's a structural improvement that makes management easier. All features remain the same!
