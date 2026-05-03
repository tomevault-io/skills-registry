---
name: fan-control-configuration
description: This skill should be used when the user asks to "configure Fan Control", "set up fan curves", "calibrate fans", "pair speed sensors", "configure GPU fans", mentions "AMD GPU fan control", "Nvidia 0 RPM", "fan curve setup", or needs guidance on Fan Control application settings, curve types, GPU-specific configurations, or troubleshooting. Use when this capability is needed.
metadata:
  author: heltonteixeira
---

# Fan Control Configuration Guide

Fan Control is a Windows application for managing system cooling through custom fan curves. This guide covers configuration, calibration, curve types, and GPU-specific settings.

## Core Concepts

### Controls and Curves

A control represents a fan or group of fans connected to a motherboard header or hub. Each control can operate in two modes:
- **Manual control**: Fixed percentage set by user
- **Curve control**: Dynamic percentage based on temperature sensor input

Assign curves via the "curve" dropdown on any control card. Select "Manual control" from the menu for fixed speed operation.

### Speed Sensor Pairing

Pair each control with its corresponding RPM sensor before calibration. Pairing provides the software context about how the control operates at any given time.

Pairing methods:
- **Manual**: Select the speed sensor from the dropdown that corresponds to the control
- **Automatic**: Use the pairing utility which spins up fans to identify matches

For fan hubs (like DeepCool FH-04), only the first port reports RPM. Map controls to hub groups rather than individual fans.

### Calibration

Calibration teaches the software how a specific fan behaves. The process measures:
- **Start point**: Minimum PWM% where the fan begins spinning
- **Stop point**: PWM% below which the fan stops
- **Min/Max speed**: RPM range the fan can achieve
- **PWM-to-RPM graph**: Complete mapping of percentage to actual speed

Calibration is required for RPM mode operation. Without calibration, curves output raw percentage values.

Reference: `references/calibration.md` for detailed calibration procedures.

## Control Parameters

Configure these parameters on each control card:

| Parameter | Purpose | Typical Use |
|-----------|---------|-------------|
| Step up % | Maximum rate of increase per update cycle | Limit rapid spin-up noise |
| Step down % | Maximum rate of decrease per update cycle | Prevent abrupt slowdowns |
| Start % | Kickstart value when resuming from 0% | Overcome fan inertia |
| Stop % | Threshold below which control snaps to 0% | Enable stop behavior |
| Offset % | Fixed adjustment to curve output | Fine-tune individual fans |
| Minimum % | Hard floor the control won't go below | Ensure minimum airflow |

For GPU controls, set Start% and Stop% to 30% to avoid the unusable 1-29% range.

Reference: `references/control-parameters.md` for parameter tuning guidance.

## Fan Curve Types

Fan Control provides seven curve types for different scenarios:

### Linear Curve

Interpolates fan speed between two temperature points. Parameters:
- Min/Max temperature: Temperature bounds for interpolation
- Min/Max speed: Fan speed percentages at temperature bounds
- Hysteresis: Minimum temperature change required for adjustment
- Response time: Minimum time between changes

Below minimum temperature, minimum speed applies. Above maximum temperature, maximum speed applies.

### Graph Curve

Custom multi-point curve using a visual editor. Left-click to add points, right-click to remove. Use "Selected Point" inputs for precise positioning. Same hysteresis and response time parameters as Linear.

### Mix Curve

Combines multiple existing curves using a function:
- **Max**: Highest value among all curves (for safety-critical cooling)
- **Min**: Lowest value (for noise optimization)
- **Average**: Mean of all curves (balanced approach)
- **Sum**: Add curve values together
- **Subtract**: Subtract subsequent curves from first

### Trigger Curve

Bistable operation with hysteresis. Maintains idle speed until load temperature reached, then switches to load speed until temperature drops to idle point.

Parameters:
- Idle/Load temperature: Switching thresholds
- Idle/Load fan speed: Output values for each state
- Response time: Delay before state changes

### Flat Curve

Fixed percentage output. Use when multiple controls need identical static speed.

### Sync Curve

Mirrors the output of another control. Parameters:
- Selected control: Control to follow
- Offset: Adjustment to applied value
- Proportional offset: Percentage-based rather than absolute offset

### Auto Curve

Self-optimizing curve that finds minimum speed to maintain target temperature. Works best under constant load. Defines idle and load zones internally, using feedback loop to adjust speed until equilibrium.

Parameters:
- Idle/Load temperature: Zone boundaries
- Min/Max fan speed: Output range limits
- Step: Rate of adjustment per response time
- Deadband: Range below load temp defining load zone

Reference: `references/fan-curves.md` for detailed curve configuration.

## GPU Configuration

GPU fan control requires special consideration due to hardware and driver limitations.

### AMD Radeon GPUs

Enable ADLX wrapper in Settings > Sensors > AdlxWrapperSettings. This is required for AMD GPU control.

Key limitations:
- **30% minimum**: GPUs refuse commands below 30% when fans are active (0% excluded for Zero RPM)
- **Zero RPM threshold**: Hardware-enforced temperature below which Zero RPM activates (typically 45-50°C, shown as dotted line in Adrenaline)
- **Overclocking reset**: Any fan command from Fan Control resets Adrenaline overclocking settings to default

Curve design for AMD:
1. Set 0% output at or below the Zero RPM temperature threshold
2. Staircase from 0% directly to 30% or higher at threshold
3. Avoid the 1-29% range entirely
4. Set control Start% and Stop% to 30%

Reference: `references/gpu-amd.md` for AMD-specific configuration.

### Nvidia RTX GPUs

Nvidia GPUs have similar 30% minimum limitation and 0 RPM restrictions.

Key limitations:
- **30% minimum**: Cannot manually set below 30%
- **0 RPM conditions**: Temperature threshold (typically 35-45°C) and power draw limits
- **Multi-monitor impact**: Additional monitors may prevent 0 RPM due to higher power draw

Workaround for 0 RPM:
1. Enable "0% hardware curve override" in Fan Control settings
2. When curve outputs 0%, Fan Control releases control to GPU
3. GPU's native automatic mode handles 0 RPM if conditions met
4. Set control Start% and Stop% to 30%
5. Design curve to output 0% only at or below GPU's native 0 RPM trigger temperature

Reference: `references/gpu-nvidia.md` for Nvidia-specific configuration.

## Custom Sensors

Create derived sensors for advanced temperature monitoring:

### Time Average Sensor

Smooths temperature readings over a configurable time period. Reduces curve response to momentary spikes.

### Mix Sensor

Combines multiple sensors using functions (Max, Min, Average, Sum, Subtract). Common use: CPU+GPU average for system exhaust fans.

### File Sensor

Reads temperature from external file (Celsius, first line, text format). Use for injecting data from unsupported sources.

### Offset Sensor

Applies fixed or proportional adjustment to existing sensor. Use for sensor calibration or compensation.

Reference: `references/custom-sensors.md` for sensor configuration details.

## Troubleshooting Quick Reference

| Issue | Likely Cause | Solution |
|-------|--------------|----------|
| Temperature sensors missing | Windows Defender blocking WinRing0 | Add Fan Control to Defender exclusions |
| GPU control not working | ADLX wrapper disabled | Enable in Settings > Sensors |
| GPU fans run despite 0% | Driver safety override | Disable/enable control to reset state |
| Hub fans not all detected | Hub limitation | Only first port reports RPM; expected |
| Phantom controls appear | Unused motherboard headers | Hide in control menu |
| Fans don't stop at 0% | Stop% not configured | Set Stop% above minimum |

Reference: `references/troubleshooting.md` for comprehensive issue resolution.

## Command Line Automation

Switch configurations programmatically:

```
FanControl.exe -c ConfigName.json
```

Common arguments:
- `-c, --config`: Load specified configuration
- `-w, --window`: Force window to open
- `-m, --minimized`: Start minimized
- `-r, --refresh`: Refresh sensors
- `-e, --exit`: Close running instance

Use shortcuts or scripts with these arguments for automation via scheduled tasks, Stream Deck, or keyboard hotkeys.

## Example Configurations

Reference: `references/example-config.md` for complete configuration examples including:
- AMD RX 6700 XT with Zero RPM
- Fan hub setup with PWM mapping
- CPU aggressive cooling curves
- System balanced exhaust curves
- Calibration data interpretation

## Best Practices

1. **Calibrate before using RPM mode**: Calibration data is required for percentage-to-RPM translation
2. **Pair sensors before calibration**: Pairing is prerequisite for calibration
3. **Use hysteresis**: Prevent fan hunting from minor temperature fluctuations
4. **Set response time**: Smooth out rapid changes, especially on descent
5. **Hide unused controls**: Reduce clutter from phantom or disconnected headers
6. **Test GPU curves carefully**: Verify Zero RPM behavior matches expectations
7. **Back up configurations**: Export before major changes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/heltonteixeira) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
