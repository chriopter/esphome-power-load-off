# Strombegrenzer

ESPHome für Athom Smart Plug V3 mit Strombegrenzung und Intervall-Timer.

## Anleitung

> **Taste** = Knopf am Gerät · **"Name"** = Entität in Home Assistant

**LED blinkt blau** → Störung
- Taste drücken → zurücksetzen
- "Sicherheitsfreigabe" EIN → zurücksetzen
- "Schalter" EIN → blockiert

**LED dauerhaft blau** → Aus
- Taste drücken → ein
- "Schalter" EIN → ein
- Timer → automatisch ein

**LED blau + rot** → An
- Taste drücken → aus
- "Schalter" AUS → aus
- Timer → automatisch aus
- Überstrom → Störung

**Taste 4s halten** → Werksreset

## Einstellungen

| Entität | Standard |
|---------|----------|
| Stromlimit | 10 A |
| Auslöseverzögerung | 200 ms |
| Intervall | Aus |
| Intervalllänge | 5 min |
| Zyklus nach Boot starten | Aus |

## Details

<details>
<summary><strong>Entitäten</strong></summary>

- Schalter, Sicherheitsfreigabe, Zyklus nach Boot starten
- Intervall, Intervalllänge, Stromlimit, Auslöseverzögerung
- Strom, Strom (Spitze), Spannung
- Leistung, Leistung (Schein), Leistung (Blind), Leistung (Faktor)
- Energie, Energie (Gesamt), Energie (Tag)
- Zyklus (verbleibend), Zyklus (seit letztem), Zyklus (bis nächster)
- Status, Störung, Betriebszeit, WLAN-Signal
- IP-Adresse, MAC-Adresse, Verbundenes WLAN, ESPHome-Version
- Neustart, Werksreset, Sicherheitsmodus, Spitzenwert zurücksetzen

</details>

<details>
<summary><strong>Hardware</strong></summary>

Athom Smart Plug V3 (ESP32-C3): GPIO3=Taste, GPIO5=Relais, GPIO6=LED, GPIO20=CSE7766

**Persistiert:** Störung, Stromlimit, Auslöseverzögerung, Intervall, Intervalllänge, Zyklus nach Boot, Energie (Gesamt)

</details>
