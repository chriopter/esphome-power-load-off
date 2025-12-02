# Athom Smart Plug V3 - Leistungsbegrenzer

ESPHome-Leistungsbegrenzer für Athom ESP32-C3 Smart Plug V3. Schaltet bei Überlast automatisch ab.

## Funktionsweise

```
┌─────────────────┐
│     NORMAL      │◄──────────────────────┐
│  Relais = AN    │                       │
│  LED = dauerhaft│                       │
└────────┬────────┘                       │
         │                                │
         │ Watt > Limit                   │ Taste drücken
         │ ODER Ampere > 16A              │
         │ ODER Taste drücken             │
         ▼                                │
┌─────────────────┐                       │
│   AUSGELÖST     │───────────────────────┘
│  Relais = AUS   │
│  LED = blinkend │
└─────────────────┘
```

- **Überlastschutz** - Trennt bei Watt-Überschreitung (0-3000W einstellbar)
- **Stromstärkeschutz** - Trennt bei >16A (YAML konfigurierbar)
- **LED-Anzeige** - Dauerlicht = AN, Blinken = ausgelöst
- **Taste** - Kurz = Auslösen/Reset, Lang 4s = Werksreset
- **Persistenz** - Zustände überleben Neustart
- **Offline-fähig** - Funktioniert ohne WiFi
- **Messung** - Alle 50ms

## Details

### Zustände

- **Normal**: Relais AN, LED dauerhaft, Taste → Auslösen
- **Ausgelöst**: Relais AUS, LED blinkt, Taste → Reset

### Boot-Reihenfolge

1. Relais startet AUS (Hardware-Sicherheit)
2. Flash-Zustand wird gelesen
3. Wenn ausgelöst → bleibt AUS, LED blinkt
4. Wenn normal → Relais AN

### Werksreset

Taste **4+ Sekunden** = löscht alles (WiFi, Einstellungen, Energie). **Vorsicht beim Reset!**

### Remote-Reset

Standardmäßig deaktiviert. Zum Aktivieren: `Reset Trip`-Button in `esphome.yaml` einkommentieren.

## Technik

### Konfiguration

```yaml
substitutions:
  name: "power-limiter"
  friendly_name: "Power Limiter"
  sensor_update_interval: 50ms
  current_limit: "16"
```

Leistungsgrenze ändern: `initial_value` in Number-Komponente anpassen.

### Hardware

Athom Smart Plug V3 (ESP32-C3): GPIO3 Taster, GPIO5 Relais, GPIO6 LED, GPIO20 CSE7766.

**Schutzgrenzen**: Leistung 0-3000W (Home Assistant), Stromstärke 16A (nur YAML).

**Flash-Persistenz**: `relay_state`, `is_tripped`, `power_limit`, `total_energy` überleben Neustart.

### Entitäten

| Entität | Beschreibung |
|---------|--------------|
| **Power Limit** | Auslöseschwelle 0-3000W (Schreibbar) |
| **Restart** | Gerät neustarten (Schreibbar) |
| **Factory Reset** | Werkseinstellungen (Schreibbar) |
| **Safe Mode** | OTA-Wiederherstellung (Schreibbar) |
| **Tripped** | Ausgelöst ja/nein |
| **Power** | Wattzahl |
| **Voltage** | Spannung (V) |
| **Current** | Stromstärke (A) |
| **Energy** | Sitzungs-kWh |
| **Total Energy** | Gesamt-kWh |
| **Total Daily Energy** | Tages-kWh |
| **Power Factor** | Leistungsfaktor |
| **Status** | Online-Status |
| **Uptime** | Betriebszeit |
| **WiFi Signal** | Signalstärke |
| **IP Address** | Netzwerk-IP |
| **MAC Address** | Geräte-MAC |
| **Connected SSID** | WiFi-Netzwerk |
| **Last Restart** | Neustart-Zeitstempel |
| **ESPHome Version** | Firmware-Version |

## Referenz

Basiert auf [athom-smart-plug.yaml](https://github.com/athom-tech/esp32-configs/blob/main/athom-smart-plug.yaml) (eingebettet, keine Abhängigkeiten).

## Lizenz

MIT
