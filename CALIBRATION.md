# Calibration Guide for NOUS D3T

This guide explains how to calibrate your NOUS D3T smart switch for accurate power measurements using the BL0942 energy monitoring chip.

## Why Calibration is Important

Each BL0942 chip and circuit can have slight variations due to:
- Component tolerances in resistors and shunts
- Manufacturing variations
- PCB layout differences
- Temperature coefficients

Proper calibration ensures your power measurements are accurate to within 1-2% of actual values.

## Tools Required

### Essential
- **Multimeter** (accurate to 0.1V for voltage)
- **Clamp meter** (accurate to 0.01A for current) OR calibrated power meter
- **Resistive load** (incandescent bulb, heater, or resistive element - NOT switch-mode power supplies)
- **Stable power source** (mains voltage)

### Optional but Recommended
- Kill-a-Watt or similar power meter for cross-reference
- Multiple known loads for verification

## Safety First! ⚠️

- **Mains voltage is dangerous** - only proceed if you're qualified
- Use proper insulated tools
- Work in a dry environment
- Double-check all connections before powering on
- Never touch exposed conductors when powered

## Calibration Process

### Step 1: Initial Installation

1. Flash the device with default calibration values:
   - `voltage_calibration: "16024.5"`
   - `current_calibration: "127074.8"`

2. Install the device and let it run for 10 minutes to stabilize

### Step 2: Voltage Calibration

**Goal**: Match ESPHome voltage reading to actual mains voltage

1. **Measure actual voltage** with your multimeter at the device
   - Take reading at the same point the device measures
   - Wait for reading to stabilize (10-30 seconds)
   - Record value (e.g., 234.5V)

2. **Check ESPHome reading** in Home Assistant or web interface
   - Record the displayed voltage (e.g., 240.2V)

3. **Calculate new calibration**:
   ```
   new_voltage_cal = current_voltage_cal × (actual_voltage / esphome_voltage)
   ```
   
   Example:
   ```
   new_voltage_cal = 16024.5 × (234.5 / 240.2)
   new_voltage_cal = 15646.1
   ```

4. **Update firmware** with new value and reflash

5. **Verify** - ESPHome reading should now match multimeter (±0.5V)

### Step 3: Current Calibration

**Goal**: Match ESPHome current reading to actual load current

1. **Connect a known resistive load**
   - Use incandescent bulb (60W-100W) or heater
   - NOT LED bulbs, phone chargers, or computers (non-resistive)
   - Turn on the load through your NOUS D3T

2. **Measure actual current** with clamp meter
   - Clamp around ONE conductor (hot or neutral, not both)
   - Wait for reading to stabilize (10-30 seconds)
   - Record value (e.g., 2.85A)

3. **Check ESPHome reading**
   - Record the displayed current (e.g., 3.12A)

4. **Calculate new calibration**:
   ```
   new_current_cal = current_current_cal × (actual_current / esphome_current)
   ```
   
   Example:
   ```
   new_current_cal = 127074.8 × (2.85 / 3.12)
   new_current_cal = 116082.5
   ```

5. **Update firmware** with new value and reflash

6. **Verify with multiple loads**:
   - 60W bulb: ~0.26A @ 230V
   - 100W bulb: ~0.43A @ 230V
   - 500W heater: ~2.17A @ 230V
   - 1000W heater: ~4.35A @ 230V

### Step 4: Power Verification

Power is calculated from voltage and current, so if those are accurate, power should be too.

**Verify** by comparing:
- ESPHome power reading
- Calculated power (V × A)
- External power meter reading (if available)

They should all match within 2-3%.

Example:
- Voltage: 230V
- Current: 2.17A
- Expected Power: 499W
- ESPHome should read: 495-503W

## Advanced Calibration Tips

### Multiple Load Points

For best accuracy across the full range:

1. Calibrate current at LOW load (~0.5A)
2. Verify at MEDIUM load (~5A)
3. Verify at HIGH load (~15A)

If readings drift at different loads, use the load point you use most often for calibration.

### Temperature Compensation

The BL0942 has built-in temperature compensation, but you may notice slight drift:

- Calibrate at normal operating temperature (after 30 minutes runtime)
- Re-check calibration in summer and winter if extreme accuracy needed

### Power Factor Consideration

The BL0942 measures real power directly, but calculated values may vary with power factor:

- **Resistive loads** (PF = 1.0): Most accurate
- **Inductive loads** (PF = 0.7-0.9): Slight variations possible
- **Capacitive loads** (PF = 0.8-0.95): May need adjustment

For mixed loads, calibrate with a resistive load but verify with your typical load.

## Calibration Worksheet

### Device Information
- Device Name: ________________
- Location: ________________
- Date: ________________
- Calibrator: ________________

### Voltage Calibration
- Initial calibration value: ________________
- Multimeter reading (actual): ________ V
- ESPHome reading (before): ________ V
- Calculated new value: ________________
- ESPHome reading (after): ________ V
- Error: ________ V (should be < 0.5V)

### Current Calibration
- Initial calibration value: ________________
- Load used: ________________ (W/description)
- Clamp meter reading (actual): ________ A
- ESPHome reading (before): ________ A
- Calculated new value: ________________
- ESPHome reading (after): ________ A
- Error: ________ A (should be < 1% of reading)

### Verification Tests
| Load | Expected V | Measured V | Expected A | Measured A | Expected W | Measured W |
|------|-----------|-----------|-----------|-----------|-----------|-----------|
| 60W  |           |           |           |           |           |           |
| 100W |           |           |           |           |           |           |
| 500W |           |           |           |           |           |           |

## Troubleshooting

### Voltage reading way off (>10V error)
- Check voltage_calibration value isn't corrupted
- Verify UART connection (TX/RX pins)
- Check for interference from logger (should be baud_rate: 0)

### Current reading always shows ~0.5A minimum
- This is normal idle consumption from the measurement circuit
- Only worry if it's >1A with no load

### Power reading negative
- This is filtered out by the `abs(x)` filter in the config
- Indicates a phase alignment issue (should not happen with BL0942)

### Readings fluctuate wildly
- Check for poor UART connection
- Verify stable power supply
- Ensure load is resistive (not switching power supply)
- Check for EMI from nearby high-power devices

### Can't get better than 5% accuracy
- Check calibration load is actually resistive
- Verify measurement equipment is accurate
- May need to check hardware (resistor values, PCB quality)
- Some batch variations may exist in components

## Batch-Specific Values

If you have multiple devices from the same batch, you may find they share similar calibration values:

| Batch ID | Typical Voltage Cal | Typical Current Cal | Notes |
|----------|-------------------|-------------------|-------|
| 2023-A   | 15980.2          | 126500.0         | Early production |
| 2023-B   | 16100.8          | 128000.5         | Mid-year batch |
| Default  | 16024.5          | 127074.8         | Factory baseline |

Keep records of your calibrations to help identify patterns!

## Long-Term Maintenance

- **Re-calibrate annually** or after any hardware changes
- **Verify quarterly** with a known load
- **Monitor drift** by comparing to utility meter readings over time

## Sharing Your Calibration

If you find consistent calibration values for a specific batch or hardware revision, please share them by:

1. Opening an issue on GitHub
2. Including your batch/hardware info
3. Providing your calibration values
4. Noting any special conditions

This helps the community and future users!

---

**Questions or Issues?**
- GitHub Issues: https://github.com/mchiriciuc/NousD3TEsphome/issues
- ESPHome Community: https://community.home-assistant.io/c/esphome/
