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

Messung alle 50ms.

### Funktionen

| Funktion | Beschreibung |
|----------|--------------|
| **Überlastschutz** | Trennt bei Watt-Überschreitung (0-3000W einstellbar) |
| **Stromstärkeschutz** | Trennt bei >16A (YAML konfigurierbar) |
| **LED-Anzeige** | Dauerlicht = AN, Blinken = ausgelöst |
| **Taste** | Kurz = Auslösen/Reset, Lang 4s = Werksreset |
| **Persistenz** | Zustände überleben Neustart |
| **Offline-fähig** | Funktioniert ohne WiFi |

### Boot-Reihenfolge

1. Relais startet AUS (Hardware-Sicherheit)
2. Flash-Zustand wird gelesen
3. Wenn ausgelöst → bleibt AUS, LED blinkt
4. Wenn normal → Relais AN

### Werksreset

Taste **4+ Sekunden** = löscht alles (WiFi, Einstellungen, Energie). **Vorsicht beim Reset!**

### Remote-Reset

Standardmäßig deaktiviert. Zum Aktivieren: `Reset Trip`-Button in `esphome.yaml` einkommentieren.

## Konfiguration

```yaml
substitutions:
  name: "power-limiter"
  friendly_name: "Power Limiter"
  sensor_update_interval: 50ms
  current_limit: "16"
```

Leistungsgrenze ändern: `initial_value` in Number-Komponente anpassen.

## Technische Details

### Hardware (Athom Smart Plug V3)

| GPIO | Funktion |
|------|----------|
| 3 | Taster |
| 5 | Relais |
| 6 | LED |
| 20 | CSE7766 (Leistungsmessung) |

### Schutzgrenzen

| Grenze | Wert | Konfigurierbar |
|--------|------|----------------|
| Leistung | 0-3000W | Home Assistant |
| Stromstärke | 16A | Nur YAML |

### Flash-Persistenz

| Wert | Beschreibung |
|------|--------------|
| `relay_state` | Relais-Zustand vor Neustart |
| `is_tripped` | Ausgelöst ja/nein |
| `power_limit` | Schwelle in Watt |
| `total_energy` | Kumulierte kWh |

### Entitäten

**Steuerung**

| Entität | Beschreibung |
|---------|--------------|
| **Power Limit** | ✏️ Auslöseschwelle (0-3000W) |
| **Restart** | ✏️ Gerät neustarten |
| **Factory Reset** | ✏️ Werkseinstellungen |
| **Safe Mode** | ✏️ OTA-Wiederherstellung |

**Status**

| Entität | Beschreibung |
|---------|--------------|
| **Tripped** | 🔴 Ausgelöst ja/nein |
| **Power** | 📊 Wattzahl |
| **Voltage** | 📊 Spannung (V) |
| **Current** | 📊 Stromstärke (A) |
| **Energy** | 📊 Sitzungs-kWh |
| **Total Energy** | 📊 Gesamt-kWh |
| **Total Daily Energy** | 📊 Tages-kWh |
| **Power Factor** | 📊 Leistungsfaktor |
| **Status** | 📊 Online-Status |
| **WiFi Signal** | 📊 Signalstärke |

## Referenz

Basiert auf [athom-smart-plug.yaml](https://github.com/athom-tech/esp32-configs/blob/main/athom-smart-plug.yaml) (eingebettet, keine Abhängigkeiten).

## Lizenz

MIT
