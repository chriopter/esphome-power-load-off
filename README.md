# Athom Smart Plug V3 - Power Limiter

Simple ESPHome power limiter for the Athom ESP32-C3 Smart Plug V3. Automatically cuts power when wattage exceeds a configurable threshold.

## Features

- **Automatic overload protection** - Cuts power when watts exceed limit
- **Visual feedback** - LED blinks when tripped, solid when power flowing
- **Physical button control** - Toggle between tripped/normal states
- **Persistent settings** - Power limit and trip state survive reboots
- **Home Assistant integration** - Full control and monitoring

## Hardware

Tested on Athom Smart Plug V3 (ESP32-C3):
- GPIO3: Button
- GPIO5: Relay
- GPIO6: LED
- GPIO20: CSE7766 (power monitoring)

## States

| State | Relay | LED | Button Action |
|-------|-------|-----|---------------|
| **Normal** | ON | Solid | → Trip |
| **Tripped** | OFF | Blinking | → Normal |

## Entities

| Entity | Type | Description |
|--------|------|-------------|
| `Relay` | Switch | Main power control |
| `Power` | Sensor | Current wattage |
| `Voltage` | Sensor | Line voltage |
| `Current` | Sensor | Current draw (A) |
| `Energy` | Sensor | Total energy (kWh) |
| `Power Limit` | Number | Trip threshold (0-3000W) |
| `Tripped` | Binary Sensor | Overload status |
| `Toggle Trip` | Button | Manual trip/reset |

## Installation

1. Copy `athom-power-limiter.yaml` to your ESPHome config directory

2. Edit substitutions if needed:
   ```yaml
   substitutions:
     name: "my-plug"
     friendly_name: "My Power Limiter"
   ```

3. Install to device:
   ```bash
   esphome run athom-power-limiter.yaml
   ```

4. In Home Assistant, set your desired power limit (default: 100W)

## Usage

### Normal operation
- LED glows solid = power is flowing
- Device monitors wattage continuously
- If power exceeds limit → automatic trip

### When tripped
- LED blinks = power is cut
- Press physical button to reset
- Or use "Toggle Trip" button in Home Assistant

### Manual trip
- Press physical button while power is flowing
- Useful for quick shutoff

## Configuration

### Change default power limit

Edit `initial_value` in the number component:
```yaml
number:
  - platform: template
    name: "${friendly_name} Power Limit"
    initial_value: 100  # Change this
```

### Adjust monitoring speed

For faster response (but more network traffic):
```yaml
sensor:
  - platform: cse7766
    # ...
    update_interval: 500ms  # Default: 1s
```

### Change blink speed

Edit delays in `led_blink_script`:
```yaml
- light.turn_on: led
- delay: 250ms  # Faster blink
- light.turn_off: led
- delay: 250ms
```

## Behavior Notes

1. **On boot**: If device was tripped before power loss, it stays tripped after reboot

2. **Home Assistant relay control**: Turning on the relay while tripped is blocked - you must reset the trip first

3. **Power limit of 0**: Setting limit to 0W will trip immediately when any load is detected

## Troubleshooting

**LED not blinking when tripped**
- Check GPIO6 pin configuration
- Verify `inverted: true` matches your hardware

**Power readings inaccurate**
- CSE7766 may need calibration for your region's voltage
- Add calibration values to the sensor config

**Trip not triggering**
- Ensure `update_interval` is fast enough to catch transients
- Check that power_limit global matches the number entity

## License

MIT - Do whatever you want with it.
