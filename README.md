# Athom Smart Plug V3 - Leistungsbegrenzer

Eigenständiger ESPHome-Leistungsbegrenzer für den Athom ESP32-C3 Smart Plug V3. Schaltet automatisch ab, wenn die Wattzahl einen konfigurierbaren Schwellenwert überschreitet.

## Referenz

Basiert auf der offiziellen Athom-Konfiguration (eingebettet, keine externen Abhängigkeiten):
- **Repository**: https://github.com/athom-tech/esp32-configs
- **Original-Config**: [athom-smart-plug.yaml](https://github.com/athom-tech/esp32-configs/blob/main/athom-smart-plug.yaml)

## Funktionen

- **Automatischer Überlastschutz** - Löst aus wenn Watt-Grenze überschritten
- **Stromstärkebegrenzung** - Löst aus wenn Strom 16A überschreitet (konfigurierbar)
- **Visuelle Rückmeldung** - LED blinkt bei Auslösung, leuchtet dauerhaft bei Stromfluss
- **Physische Tastenbedienung** - Kurz drücken: Relais umschalten/Reset, Lang drücken (4s): Werksreset
- **Persistenter Zustand** - Auslösezustand, Leistungsgrenze und Energiesummen überleben Neustarts
- **Weboberfläche** - Eingebautes Konfigurationsportal auf Port 80

## Hardware

Athom Smart Plug V3 (ESP32-C3):

| GPIO | Funktion |
|------|----------|
| 3 | Taster (INPUT_PULLUP, invertiert) |
| 5 | Relais |
| 6 | LED (invertiert) |
| 20 | CSE7766 RX (Leistungsmessung) |

## Zustände

| Zustand | Relais | LED | Kurzer Tastendruck |
|---------|--------|-----|-------------------|
| Normal | AN | Dauerlicht | Relais umschalten |
| Ausgelöst | AUS | Blinkend | Reset |

## Entitäten

### Leistungsbegrenzer

| Entität | Typ | Beschreibung |
|---------|-----|--------------|
| Power Limit | Number | Auslöseschwelle (0-3000W, Standard 100W) |
| Tripped | Binary Sensor | Überlast-Status |

### Leistungsmessung

| Entität | Typ | Beschreibung |
|---------|-----|--------------|
| Switch | Switch | Hauptrelais-Steuerung |
| Power | Sensor | Aktuelle Wattzahl |
| Voltage | Sensor | Netzspannung |
| Current | Sensor | Stromstärke (A) |
| Energy | Sensor | Sitzungs-Energie (kWh) |
| Total Energy | Sensor | Persistente Gesamtenergie (kWh) |
| Total Daily Energy | Sensor | Tagesverbrauch |
| Apparent Power | Sensor | Scheinleistung (VA) |
| Reactive Power | Sensor | Blindleistung (VAR) |
| Power Factor | Sensor | Leistungsfaktor |

### Gerätestatus

| Entität | Typ | Beschreibung |
|---------|-----|--------------|
| Status | Binary Sensor | Online-Status |
| Power Button | Binary Sensor | Physische Taste (standardmäßig deaktiviert) |
| Uptime Sensor | Sensor | Geräte-Betriebszeit |
| WiFi Signal dB | Sensor | Signalstärke (dBm) |
| WiFi Signal Percent | Sensor | Signalstärke (%) |
| IP Address | Text Sensor | Netzwerk-IP |
| Connected SSID | Text Sensor | WiFi-Netzwerk |
| Mac Address | Text Sensor | Geräte-MAC |
| Last Restart | Text Sensor | Neustart-Zeitstempel |
| Status LED | Light | Blaue LED-Steuerung (standardmäßig deaktiviert) |

### Konfiguration

| Entität | Typ | Beschreibung |
|---------|-----|--------------|
| Restart | Button | Gerät neustarten |
| Factory Reset | Button | Auf Werkseinstellungen zurücksetzen |
| Safe Mode | Button | OTA-Wiederherstellungsmodus |

## Installation

1. `esphome.yaml` in dein ESPHome-Konfigurationsverzeichnis kopieren

2. Substitutions anpassen:
   ```yaml
   substitutions:
     name: "mein-stecker"
     friendly_name: "Mein Leistungsbegrenzer"
   ```

3. Installieren:
   ```bash
   esphome run esphome.yaml
   ```

4. Leistungsgrenze in Home Assistant setzen (Standard: 100W)

## Verwendung

### Normalbetrieb
- LED leuchtet dauerhaft = Strom fließt
- Leistung wird alle 10s gemessen (konfigurierbar)
- Überschreitung = automatische Auslösung

### Bei Auslösung
- LED blinkt = Strom unterbrochen
- Kurz Taste drücken zum Zurücksetzen

### Manuelle Bedienung
- Kurzer Tastendruck = Relais umschalten (wenn nicht ausgelöst)

### Werksreset (Vorsicht!)
- Taste **4+ Sekunden** lang drücken = Werksreset
- **Löscht alles**: WiFi-Zugangsdaten, Leistungsgrenze, Energiesummen, alle gespeicherten Zustände
- Gerät startet im AP-Modus für Neueinrichtung
- **Versehentliches langes Drücken beim Reset vermeiden!**

### Remote-Reset aktivieren
Standardmäßig ist der "Reset Trip"-Button **deaktiviert** - Reset nur über physische Taste möglich. Um Remote-Reset aus Home Assistant zu aktivieren, den `Reset Trip`-Button in `esphome.yaml` einkommentieren.

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

## Flash-Persistenz

Diese Werte überleben Neustarts (im ESP32-Flash gespeichert):

| Wert | Beschreibung |
|------|--------------|
| `relay_state` | War Relais vor Neustart AN? |
| `is_tripped` | Ist Gerät ausgelöst? |
| `power_limit` | Auslöseschwelle in Watt |
| `total_energy` | Kumulierte kWh |

## Schutzgrenzen

| Grenze | Wert | Konfigurierbar |
|--------|------|----------------|
| Leistung | 0-3000W | Ja (Home Assistant) |
| Stromstärke | 16A | Nur YAML (`current_limit` substitution) |

Stromstärkegrenze ist eine **Hardware-Sicherheit** - schützt vor Überstrom auch wenn Watt-Berechnung fehlschlägt. 16A ist typisches Maximum für EU-Steckdosen.

## Verhaltenshinweise

1. **Sicherer Boot**: Relais-Hardware startet AUS, dann wird gespeicherter Zustand aus Flash wiederhergestellt
2. **Auslöse-Priorität**: Wenn ausgelöst, bleibt Relais AUS unabhängig vom gespeicherten Zustand
3. **Relais-Schutz**: Home Assistant kann Relais nicht einschalten wenn ausgelöst
4. **Doppelter Schutz**: Löst bei Wattzahl ODER Stromstärke-Überschreitung aus
5. **Null-Grenze**: 0W einstellen löst bei jeder Last aus (Leistung > 3W wegen Rauschfilter)

## Offline-Betrieb

Funktioniert **vollständig ohne WiFi**. Wenn kein bekanntes Netzwerk gefunden wird, öffnet der Stecker einen eigenen Access Point (`power-limiter`) für Konfiguration. Alle Kernfunktionen (Überlastschutz, Zustandswiederherstellung, LED-Anzeige) sind lokal.

## Lizenz

MIT
