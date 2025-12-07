# Athom Smart Plug V3 - Strombegrenzer

ESPHome-Firmware für Athom ESP32-C3 Smart Plug V3 mit automatischer Abschaltung bei Stromüberschreitung.

## Funktionsweise

<details>
<summary><strong>Programmablaufplan (DIN 66001)</strong></summary>

```
══════════════════════════════════════════════════════════════════════════════
                              INITIALISIERUNG
══════════════════════════════════════════════════════════════════════════════

                              ╭───────────╮
                              │   START   │
                              ╰─────┬─────╯
                                    │
                                    ▼
                      ┌───────────────────────────┐
                      │   Lade aus Flash (NVS):   │
                      │   • relay_state           │
                      │   • current_limit         │
                      │   • total_energy          │
                      └─────────────┬─────────────┘
                                    │
                                    ▼
                               ╱────────╲
                              ╱  Relais  ╲ ───Nein──▶┌─────────────────┐
                              ╲   AN?    ╱           │ Starte Blinken  │
                               ╲────────╱            └────────┬────────┘
                                    │ Ja                      │
                                    ▼                         │
                           ┌──────────────┐                   │
                           │  LED = AN    │                   │
                           └───────┬──────┘                   │
                                   │                          │
                                   └────────────┬─────────────┘
                                                │
                                                ▼
                                     ╭─────────────────╮
                                     │ BEREIT / WARTEN │
                                     ╰─────────────────╯


══════════════════════════════════════════════════════════════════════════════
                          EVENT: STROMMESSUNG (50ms)
══════════════════════════════════════════════════════════════════════════════

              ╭─────────────────────╮
              │ CSE7766 liefert     │
              │ neuen Messwert      │
              ╰──────────┬──────────╯
                         │
                         ▼
              ╱─────────────────────╲
             ╱   Relais = AN         ╲
            ╱    UND                  ╲───Nein──▶╭───────╮
            ╲    Strom > Limit?       ╱          │ Ende  │
             ╲───────────────────────╱           ╰───────╯
                         │ Ja
                         ▼
              ┌─────────────────────┐
              │ Log: "OVERLOAD:     │
              │ x.xxA > y.yA - OFF" │
              └──────────┬──────────┘
                         │
                         ▼
              ┌─────────────────────┐
              │   Relais → AUS      │───────────▶ (löst EVENT:
              └─────────────────────┘               RELAIS AUS aus)


══════════════════════════════════════════════════════════════════════════════
                             EVENT: TASTENDRUCK
══════════════════════════════════════════════════════════════════════════════

                         ╭─────────────────╮
                         │ Taste gedrückt  │
                         ╰────────┬────────╯
                                  │
                                  ▼
                         ╱────────────────╲
                        ╱   Dauer der       ╲
                       ╱    Betätigung?      ╲
                       ╲                     ╱
                        ╲───────────────────╱
                           │            │
               < 1s        │            │  ≥ 4s
              (kurz)       │            │ (lang)
                           ▼            ▼
                  ╱─────────────╲    ┌────────────────────┐
                 ╱  Relais AN?   ╲   │   Factory Reset    │
                 ╲               ╱   └─────────┬──────────┘
                  ╲─────────────╱              │
                    │         │                ▼
                Ja  │         │ Nein    ╭────────────╮
                    ▼         ▼         │ Neustart   │
          ┌───────────┐  ┌───────────┐  ╰────────────╯
          │ Relais    │  │ Relais    │
          │  → AUS    │  │  → AN     │
          └─────┬─────┘  └─────┬─────┘
                │              │
                ▼              ▼
          ╭──────────╮   ╭──────────╮
          │ EVENT:   │   │ EVENT:   │
          │ RELAIS   │   │ RELAIS   │
          │ AUS      │   │ AN       │
          ╰──────────╯   ╰──────────╯


══════════════════════════════════════════════════════════════════════════════
                             EVENT: RELAIS AN
══════════════════════════════════════════════════════════════════════════════

                         ╭─────────────────╮
                         │ Relais → AN     │
                         ╰────────┬────────╯
                                  │
                                  ▼
                      ┌───────────────────────┐
                      │ Stoppe Blink-Script   │
                      └───────────┬───────────┘
                                  │
                                  ▼
                      ┌───────────────────────┐
                      │ LED = AN (Dauerlicht) │
                      └───────────┬───────────┘
                                  │
                                  ▼
                              ╭───────╮
                              │ Ende  │
                              ╰───────╯


══════════════════════════════════════════════════════════════════════════════
                             EVENT: RELAIS AUS
══════════════════════════════════════════════════════════════════════════════

                         ╭─────────────────╮
                         │ Relais → AUS    │
                         ╰────────┬────────╯
                                  │
                                  ▼
                   ┌──────────────────────────────┐
                   │     Starte Blink-Script      │
                   └──────────────┬───────────────┘
                                  │
                                  ▼
                   ┌──────────────────────────────┐
                   │ ╔══════════════════════════╗ │
                   │ ║  WHILE Relais = AUS      ║ │
              ┌───▶│ ╚══════════════════════════╝ │
              │    └──────────────┬───────────────┘
              │                   │
              │                   ▼
              │          ┌──────────────┐
              │          │  LED = AN    │
              │          └───────┬──────┘
              │                  │
              │                  ▼
              │          ┌──────────────┐
              │          │ Warte 500ms  │
              │          └───────┬──────┘
              │                  │
              │                  ▼
              │          ┌──────────────┐
              │          │  LED = AUS   │
              │          └───────┬──────┘
              │                  │
              │                  ▼
              │          ┌──────────────┐
              │          │ Warte 500ms  │
              │          └───────┬──────┘
              │                  │
              └──────────────────┘

                    (Schleife endet wenn Relais → AN)


══════════════════════════════════════════════════════════════════════════════
                            LEGENDE (DIN 66001)
══════════════════════════════════════════════════════════════════════════════

    ╭─────────╮
    │         │     Grenzstelle (Start / Ende / Ereignis)
    ╰─────────╯

    ┌─────────┐
    │         │     Operation / Verarbeitung
    └─────────┘

       ╱───╲
      ╱     ╲       Verzweigung / Entscheidung
      ╲     ╱
       ╲───╱

    ╔═════════╗
    ║  WHILE  ║     Schleifenbedingung
    ╚═════════╝

    ─────▶          Ablauflinie mit Richtung

    ──┐
      │             Rücksprung (Schleife)
    ◀─┘
```

</details>

- **Stromstärkegrenze** - Schaltet ab bei Ampere-Überschreitung (0-16A einstellbar)
- **LED-Anzeige** - Dauerhaft = AN, Blinken = AUS
- **Taste** - Kurz = Ein/Aus, Lang 4s = Werksreset
- **Persistenz** - Einstellungen überleben Neustart (siehe Hardware)
- **Offline-fähig** - Funktioniert ohne WiFi
- **Messung** - Alle 50ms
- **Erweiterbar** - Zeitschaltuhr oder Remote-Einschaltung kann eingebaut werden, wenn mehr als Sicherheitsabschaltung gewünscht

## Details

<details>
<summary><strong>Konfiguration</strong></summary>

```yaml
substitutions:
  name: "power-limiter"
  friendly_name: "Power Limiter"
  sensor_update_interval: 50ms
```

Stromstärkegrenze ändern: `initial_value` in Number-Komponente oder via Home Assistant (0-16A, Standard: 10A).

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

**Flash-Persistenz** (überleben Neustart):

| Wert | Beschreibung |
|------|--------------|
| Schaltzustand | AN/AUS |
| `current_limit` | Schwelle in Ampere |
| `total_energy` | Kumulierte kWh |

</details>

<details>
<summary><strong>Entitäten</strong></summary>

**Schreibbar**

| Entität | Beschreibung |
|---------|--------------|
| Current Limit | Auslöseschwelle 0-16A |
| Restart | Gerät neustarten |
| Factory Reset | Werkseinstellungen |
| Safe Mode | OTA-Wiederherstellung |

> **Sicherheit:** Der Schalter ist standardmäßig nur per Taste bedienbar (nicht via Home Assistant). Für Remote-Steuerung: `name: "Power"` in `esphome.yaml` einkommentieren und `internal: true` entfernen.

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
