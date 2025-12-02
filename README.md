# Athom Smart Plug V3 - Leistungsbegrenzer

Eigenständiger ESPHome-Leistungsbegrenzer für den Athom ESP32-C3 Smart Plug V3. Schaltet automatisch ab, wenn die Wattzahl einen konfigurierbaren Schwellenwert überschreitet.

## Referenz

Basiert auf der offiziellen Athom-Konfiguration (eingebettet, keine externen Abhängigkeiten):
- **Repository**: https://github.com/athom-tech/esp32-configs
- **Original-Config**: [athom-smart-plug.yaml](https://github.com/athom-tech/esp32-configs/blob/main/athom-smart-plug.yaml)

## Funktionsweise

```
                    ┌─────────────────────────────────────┐
                    │           STROMFLUSS                │
                    └─────────────────────────────────────┘
                                    │
                                    ▼
                    ┌─────────────────────────────────────┐
                    │      Leistungsmessung (alle 10s)    │
                    │         Watt / Ampere / Volt        │
                    └─────────────────────────────────────┘
                                    │
                         ┌──────────┴──────────┐
                         ▼                     ▼
                ┌─────────────────┐   ┌─────────────────┐
                │  Watt > Limit?  │   │ Ampere > 16A?   │
                └─────────────────┘   └─────────────────┘
                         │                     │
                         └──────────┬──────────┘
                                    │ JA
                                    ▼
                    ┌─────────────────────────────────────┐
                    │         🚨 AUSLÖSUNG 🚨             │
                    │    • Relais AUS (Strom getrennt)    │
                    │    • LED blinkt                     │
                    │    • Zustand im Flash gespeichert   │
                    └─────────────────────────────────────┘
                                    │
                                    ▼
                    ┌─────────────────────────────────────┐
                    │     Taste kurz drücken = RESET      │
                    │    • Relais AN (Strom fließt)       │
                    │    • LED leuchtet dauerhaft         │
                    └─────────────────────────────────────┘
```

| Funktion | Beschreibung |
|----------|--------------|
| **Überlastschutz** | Trennt Strom wenn Watt-Grenze überschritten (einstellbar 0-3000W) |
| **Stromstärkeschutz** | Trennt Strom wenn >16A (Hardware-Sicherheit, YAML konfigurierbar) |
| **LED-Anzeige** | Dauerlicht = Strom fließt, Blinken = ausgelöst |
| **Tastenbedienung** | Kurz = Reset/Umschalten, Lang (4s) = Werksreset |
| **Persistenz** | Alle Zustände überleben Stromausfall/Neustart |
| **Offline-fähig** | Funktioniert komplett ohne WiFi/Internet |

### Zustände

| Zustand | Relais | LED | Kurzer Tastendruck |
|---------|--------|-----|-------------------|
| Normal | AN | Dauerlicht | Relais umschalten |
| Ausgelöst | AUS | Blinkend | Reset |

### Werksreset (Vorsicht!)

- Taste **4+ Sekunden** lang drücken = Werksreset
- **Löscht alles**: WiFi-Zugangsdaten, Leistungsgrenze, Energiesummen, alle gespeicherten Zustände
- Gerät startet im AP-Modus für Neueinrichtung
- **Versehentliches langes Drücken beim Reset vermeiden!**

### Remote-Reset aktivieren

Standardmäßig ist der "Reset Trip"-Button **deaktiviert** - Reset nur über physische Taste möglich. Um Remote-Reset aus Home Assistant zu aktivieren, den `Reset Trip`-Button in `esphome.yaml` einkommentieren.

## Hardware

Athom Smart Plug V3 (ESP32-C3):

| GPIO | Funktion |
|------|----------|
| 3 | Taster (INPUT_PULLUP, invertiert) |
| 5 | Relais |
| 6 | LED (invertiert) |
| 20 | CSE7766 RX (Leistungsmessung) |

## Entitäten

### Steuerung (Eingabe möglich)

| Entität | Typ | Beschreibung |
|---------|-----|--------------|
| **Power Limit** | Number | ✏️ Auslöseschwelle einstellen (0-3000W) |
| **Switch** | Switch | ✏️ Relais ein/ausschalten |
| **Restart** | Button | ✏️ Gerät neustarten |
| **Factory Reset** | Button | ✏️ Werkseinstellungen |
| **Safe Mode** | Button | ✏️ OTA-Wiederherstellungsmodus |
| **Status LED** | Light | ✏️ Blaue LED steuern (Standard: deaktiviert) |

### Status (nur lesen)

| Entität | Typ | Beschreibung |
|---------|-----|--------------|
| **Tripped** | Binary Sensor | 🔴 Ausgelöst ja/nein |
| **Power** | Sensor | 📊 Aktuelle Wattzahl |
| **Voltage** | Sensor | 📊 Netzspannung (V) |
| **Current** | Sensor | 📊 Stromstärke (A) |
| **Energy** | Sensor | 📊 Sitzungs-Energie (kWh) |
| **Total Energy** | Sensor | 📊 Gesamtenergie persistent (kWh) |
| **Total Daily Energy** | Sensor | 📊 Tagesverbrauch (kWh) |
| **Apparent Power** | Sensor | 📊 Scheinleistung (VA) |
| **Reactive Power** | Sensor | 📊 Blindleistung (VAR) |
| **Power Factor** | Sensor | 📊 Leistungsfaktor |
| **Status** | Binary Sensor | 📊 Online-Status |
| **Uptime Sensor** | Sensor | 📊 Betriebszeit |
| **WiFi Signal dB** | Sensor | 📊 Signalstärke (dBm) |
| **WiFi Signal Percent** | Sensor | 📊 Signalstärke (%) |
| **IP Address** | Text Sensor | 📊 Netzwerk-IP |
| **Connected SSID** | Text Sensor | 📊 WiFi-Netzwerk |
| **Mac Address** | Text Sensor | 📊 Geräte-MAC |
| **Last Restart** | Text Sensor | 📊 Neustart-Zeitstempel |
| **Power Button** | Binary Sensor | 📊 Physische Taste (Standard: deaktiviert) |

## Konfiguration

Alle Einstellungen im `substitutions`-Bereich:

```yaml
substitutions:
  name: "power-limiter"
  friendly_name: "Power Limiter"
  sensor_update_interval: 10s    # Messintervall
  current_limit: "16"            # Max Ampere vor Auslösung
  relay_restore_mode: DISABLED   # Wir steuern Wiederherstellung in on_boot
  power_plug_type: "power-socket-eu"  # Icon-Typ
```

### Standard-Leistungsgrenze ändern

`initial_value` in der Number-Komponente anpassen:

```yaml
number:
  - platform: template
    name: "Power Limit"
    initial_value: 200  # Von 100 ändern
```

## Technische Details

### Flash-Persistenz

Diese Werte überleben Neustarts (im ESP32-Flash gespeichert):

| Wert | Beschreibung |
|------|--------------|
| `relay_state` | War Relais vor Neustart AN? |
| `is_tripped` | Ist Gerät ausgelöst? |
| `power_limit` | Auslöseschwelle in Watt |
| `total_energy` | Kumulierte kWh |

### Schutzgrenzen

| Grenze | Wert | Konfigurierbar |
|--------|------|----------------|
| Leistung | 0-3000W | Ja (Home Assistant) |
| Stromstärke | 16A | Nur YAML (`current_limit` substitution) |

Stromstärkegrenze ist eine **Hardware-Sicherheit** - schützt vor Überstrom auch wenn Watt-Berechnung fehlschlägt. 16A ist typisches Maximum für EU-Steckdosen.

### Verhalten

| Situation | Verhalten |
|-----------|-----------|
| **Boot** | Relais startet AUS, dann wird Flash-Zustand wiederhergestellt |
| **Boot wenn ausgelöst** | Relais bleibt AUS, LED blinkt |
| **Ausgelöst + HA schaltet ein** | Wird blockiert, Relais bleibt AUS |
| **Watt ODER Ampere überschritten** | Löst aus (doppelter Schutz) |
| **Limit auf 0W** | Löst bei jeder Last >3W aus (Rauschfilter) |
| **Kein WiFi** | Funktioniert lokal, öffnet AP (`power-limiter`) |

## Lizenz

MIT
