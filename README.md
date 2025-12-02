# Athom Smart Plug V3 - Power Limiter

Self-contained ESPHome power limiter for the Athom ESP32-C3 Smart Plug V3. Automatically cuts power when wattage exceeds a configurable threshold.

## Reference

Based on the official Athom configuration (vendored, no external dependencies):
- **Repository**: https://github.com/athom-tech/esp32-configs
- **Original config**: [athom-smart-plug.yaml](https://github.com/athom-tech/esp32-configs/blob/main/athom-smart-plug.yaml)

## Features

- **Automatic overload protection** - Trips when watts exceed limit
- **Current limit protection** - Trips when current exceeds 16A (configurable)
- **Visual feedback** - LED blinks when tripped, solid when power flowing
- **Physical button control** - Short press: toggle relay/reset trip, Long press (4s): factory reset
- **Persistent state** - Trip state, power limit, and energy totals survive reboots
- **Web interface** - Built-in config portal on port 80

## Hardware

Athom Smart Plug V3 (ESP32-C3):

| GPIO | Function |
|------|----------|
| 3 | Button (INPUT_PULLUP, inverted) |
| 5 | Relay |
| 6 | LED (inverted) |
| 20 | CSE7766 RX (power monitoring) |

## States

| State | Relay | LED | Short Button Press |
|-------|-------|-----|-------------------|
| Normal | ON | Solid | Toggle relay |
| Tripped | OFF | Blinking | Reset trip |

## Entities

### Power Limiter

| Entity | Type | Description |
|--------|------|-------------|
| Power Limit | Number | Trip threshold (0-3000W, default 100W) |
| Tripped | Binary Sensor | Overload status |

### Power Monitoring

| Entity | Type | Description |
|--------|------|-------------|
| Switch | Switch | Main relay control |
| Power | Sensor | Current wattage |
| Voltage | Sensor | Line voltage |
| Current | Sensor | Current draw (A) |
| Energy | Sensor | Session energy (kWh) |
| Total Energy | Sensor | Persistent total (kWh) |
| Total Daily Energy | Sensor | Daily consumption |
| Apparent Power | Sensor | VA |
| Reactive Power | Sensor | VAR |
| Power Factor | Sensor | PF |

### Device Status

| Entity | Type | Description |
|--------|------|-------------|
| Status | Binary Sensor | Online status |
| Power Button | Binary Sensor | Physical button (disabled by default) |
| Uptime Sensor | Sensor | Device uptime |
| WiFi Signal dB | Sensor | Signal strength (dBm) |
| WiFi Signal Percent | Sensor | Signal strength (%) |
| IP Address | Text Sensor | Network IP |
| Connected SSID | Text Sensor | WiFi network |
| Mac Address | Text Sensor | Device MAC |
| Last Restart | Text Sensor | Restart timestamp |
| Status LED | Light | Blue LED control (disabled by default) |

### Configuration

| Entity | Type | Description |
|--------|------|-------------|
| Restart | Button | Reboot device |
| Factory Reset | Button | Reset to defaults |
| Safe Mode | Button | OTA recovery mode |

## Installation

1. Copy `esphome.yaml` to your ESPHome config directory

2. Edit substitutions:
   ```yaml
   substitutions:
     name: "my-plug"
     friendly_name: "My Power Limiter"
   ```

3. Install:
   ```bash
   esphome run esphome.yaml
   ```

4. Set power limit in Home Assistant (default: 100W)

## Usage

### Normal Operation
- LED solid = power flowing
- Power monitored every 10s (configurable)
- Exceeds limit = automatic trip

### When Tripped
- LED blinks = power cut
- Short press button or use "Reset Trip" in Home Assistant

### Manual Control
- Short press button = toggle relay (when not tripped)

### Factory Reset (caution!)
- Long press button **4+ seconds** = factory reset
- **Clears everything**: WiFi credentials, power limit, energy totals, all saved state
- Device reboots into AP mode for fresh setup
- **Avoid accidental long presses when resetting trip!**

### Enable Remote Reset Trip
By default, the "Reset Trip" button is **disabled** - trip can only be reset via physical button. To enable remote reset from Home Assistant, uncomment the `Reset Trip` button in `esphome.yaml`.

## Configuration

All settings are in the `substitutions` section:

```yaml
substitutions:
  name: "power-limiter"
  friendly_name: "Power Limiter"
  sensor_update_interval: 10s    # Power monitoring frequency
  current_limit: "16"            # Max amps before trip
  relay_restore_mode: DISABLED          # We handle restore in on_boot
  power_plug_type: "power-socket-eu"  # Icon type
```

### Change Default Power Limit

Edit `initial_value` in the number component:

```yaml
number:
  - platform: template
    name: "Power Limit"
    initial_value: 200  # Change from 100
```

## Flash Persistence

These values survive reboots (saved to ESP32 flash):

| Value | Description |
|-------|-------------|
| `relay_state` | Was relay ON before reboot? |
| `is_tripped` | Is device in tripped state? |
| `power_limit` | Trip threshold in watts |
| `total_energy` | Accumulated kWh |

## Protection Limits

| Limit | Value | Configurable |
|-------|-------|--------------|
| Power | 0-3000W | Yes (Home Assistant) |
| Current | 16A | YAML only (`current_limit` substitution) |

Current limit is a **hardware safety** - protects against overcurrent even if wattage calculation fails. 16A is typical max for EU plugs.

## Behavior Notes

1. **Safe boot**: Relay hardware starts OFF, then restores saved state from flash
2. **Trip priority**: If tripped, relay stays OFF regardless of saved state
3. **Relay protection**: Home Assistant cannot turn on relay while tripped
4. **Dual protection**: Trips on either wattage OR current limit exceeded
5. **Zero limit**: Setting 0W trips on any load detection (power > 3W due to noise filter)

## License

MIT
