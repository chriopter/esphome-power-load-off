# Athom Smart Plug V3 - Leistungsbegrenzer

ESPHome-Firmware für Athom ESP32-C3 Smart Plug V3 mit automatischer Abschaltung bei Überlast.

## Funktionsweise

```
┌─────────────────┐
│       AN        │◄──────────────────┐
│  Strom fließt   │                   │
│  LED = an       │                   │
└────────┬────────┘                   │
         │                            │
         │ Watt > Limit               │ Taste oder
         │ ODER Ampere > 16A          │ Home Assistant
         │ ODER Taste                 │
         ▼                            │
┌─────────────────┐                   │
│       AUS       │───────────────────┘
│  Kein Strom     │
│  LED = blinkt   │
└─────────────────┘
```

- **Leistungsgrenze** - Schaltet ab bei Watt-Überschreitung (0-3000W einstellbar)
- **Stromstärkegrenze** - Schaltet ab bei >16A (YAML konfigurierbar)
- **LED-Anzeige** - Dauerhaft = AN, Blinken = AUS
- **Taste** - Kurz = Ein/Aus, Lang 4s = Werksreset
- **Persistenz** - Schaltzustand überlebt Neustart
- **Offline-fähig** - Funktioniert ohne WiFi
- **Messung** - Alle 50ms

## Details

<details>
<summary><strong>Konfiguration</strong></summary>

```yaml
substitutions:
  name: "power-limiter"
  friendly_name: "Power Limiter"
  sensor_update_interval: 50ms
  current_limit: "16"
```

Leistungsgrenze ändern: `initial_value` in Number-Komponente oder via Home Assistant.

</details>

<details>
<summary><strong>Hardware</strong></summary>

Athom Smart Plug V3 (ESP32-C3):

| GPIO | Funktion |
|------|----------|
| 3 | Taster |
| 5 | Relais |
| 6 | LED |
| 20 | CSE7766 |

**Schutzgrenzen**: Leistung 0-3000W (Home Assistant), Stromstärke 16A (nur YAML).

**Flash-Persistenz** (überleben Neustart):

| Wert | Beschreibung |
|------|--------------|
| Schaltzustand | AN/AUS |
| `power_limit` | Schwelle in Watt |
| `total_energy` | Kumulierte kWh |

</details>

<details>
<summary><strong>Entitäten</strong></summary>

**Schreibbar**

| Entität | Beschreibung |
|---------|--------------|
| Power | Schalter Ein/Aus |
| Power Limit | Auslöseschwelle 0-3000W |
| Restart | Gerät neustarten |
| Factory Reset | Werkseinstellungen |
| Safe Mode | OTA-Wiederherstellung |

**Messwerte**

| Entität | Beschreibung |
|---------|--------------|
| Power Off | Problem-Anzeige wenn AUS |
| Power (Sensor) | Wattzahl |
| Voltage | Spannung (V) |
| Current | Stromstärke (A) |
| Energy | Sitzungs-kWh |
| Total Energy | Gesamt-kWh |
| Total Daily Energy | Tages-kWh |
| Power Factor | Leistungsfaktor |
| Apparent Power | Scheinleistung (VA) |
| Reactive Power | Blindleistung (var) |

**Geräteinfo**

| Entität | Beschreibung |
|---------|--------------|
| Status | Online-Status |
| Uptime | Betriebszeit |
| WiFi Signal | Signalstärke |
| IP Address | Netzwerk-IP |
| MAC Address | Geräte-MAC |
| Connected SSID | WiFi-Netzwerk |
| Last Restart | Neustart-Zeitstempel |
| ESPHome Version | Firmware-Version |

</details>

## Referenz & Lizenz

Basiert auf [athom-smart-plug.yaml](https://github.com/athom-tech/esp32-configs/blob/main/athom-smart-plug.yaml) (eingebettet, keine Abhängigkeiten). MIT Lizenz.
