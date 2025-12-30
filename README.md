# Power Limiter

ESPHome firmware for Athom Smart Plug V3 with current-based power limiting and interval timer.

## Behavior

- **Overcurrent detected:** Relay turns off after trip delay, fault state activates, blue LED blinks until manual reset
- **Fault reset:** Short button press or "Sicherheitsfreigabe" switch clears fault and starts a new cycle
- **Interval timer active:** Relay turns on for configured run duration, then off until next interval
- **Start After Boot enabled:** First cycle starts immediately on boot instead of waiting for interval
- **Manual power on during fault:** Blocked until fault is cleared
- **Long button press (4s):** Factory reset
- **Blue LED solid:** System OK / ready
- **Blue LED blinking:** Fault active (overcurrent tripped)
- **Red LED:** Hardwired to relay state

## Details

<details>
<summary><strong>Konfiguration</strong></summary>

```yaml
substitutions:
  name: "power-limiter"
  friendly_name: "Power Limiter"
  sensor_update_interval: 10s
```

Einstellbar via Home Assistant:
- **Current Limit:** 0-16A (Standard: 10A)
- **Trip Delay:** 0-5000ms (Standard: 200ms)
- **Interval:** Aus, 1h-24h
- **Run Duration:** 1-30 min (Standard: 5 min)

</details>

<details>
<summary><strong>Hardware</strong></summary>

Athom Smart Plug V3 (ESP32-C3):

| GPIO | Funktion |
|------|----------|
| 3 | Taster |
| 5 | Relais |
| 6 | Blaue LED |
| 20 | CSE7766 RX |

Rote LED ist hardwired mit Relais.

**Flash-Persistenz** (überleben Neustart):

| Wert | Beschreibung |
|------|--------------|
| `current_limit` | Schwelle in Ampere |
| `trip_delay` | Verzögerung in ms |
| `timer_interval` | Intervall-Einstellung |
| `run_duration` | Laufzeit-Einstellung |
| `start_after_boot_flag` | Sofortstart nach Boot |
| `total_energy` | Kumulierte kWh |

</details>

<details>
<summary><strong>Entitäten</strong></summary>

**Steuerung**

| Entität | Beschreibung |
|---------|--------------|
| Power | Relais ein/aus (blockiert bei Fault) |
| Sicherheitsfreigabe | Fault zurücksetzen / manuell sperren |
| Start Cycle After Boot | Sofortstart wenn Timer aktiv |
| Interval | Timer-Intervall (Aus, 1h-24h) |
| Run Duration | Laufzeit pro Zyklus (1-30 min) |
| Current Limit | Auslöseschwelle 0-16A |
| Trip Delay | Verzögerung vor Auslösung 0-5000ms |

**Messwerte**

| Entität | Beschreibung |
|---------|--------------|
| Current | Stromstärke (A) |
| Voltage | Spannung (V) |
| Power | Leistung (W) |
| Energy | Sitzungs-kWh |
| Total Energy | Gesamt-kWh (persistent) |
| Total Daily Energy | Tages-kWh |
| Power Factor | Leistungsfaktor |
| Apparent Power | Scheinleistung (VA) |
| Reactive Power | Blindleistung (var) |
| Peak Current | Höchster gemessener Strom |
| Minutes Since Last Cycle | Zeit seit letztem Zyklusstart |
| Minutes Until Next Cycle | Zeit bis nächster Zyklus |

**System**

| Entität | Beschreibung |
|---------|--------------|
| Status | Online-Status |
| Uptime | Betriebszeit |
| WiFi Signal | Signalstärke |
| IP Address | Netzwerk-IP |
| MAC Address | Geräte-MAC |
| Connected SSID | WiFi-Netzwerk |
| ESPHome Version | Firmware-Version |
| Restart | Gerät neustarten |
| Factory Reset | Werkseinstellungen |
| Safe Mode | OTA-Wiederherstellung |
| Reset Peak | Peak Current zurücksetzen |

</details>
