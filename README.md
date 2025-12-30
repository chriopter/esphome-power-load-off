# Strombegrenzer

ESPHome für Athom Smart Plug V3 mit Strombegrenzung und Intervall-Timer.

## Was passiert wenn...

| Aktion | Ergebnis |
|--------|----------|
| **Strom > Limit** | Relais AUS, Störung aktiv, LED blinkt |
| **Taste kurz (bei Störung)** | Störung zurücksetzen, LED dauerhaft |
| **Taste kurz (normal)** | Relais toggeln |
| **Taste lang (4s)** | Werksreset |
| **Schalter EIN (bei Störung)** | Blockiert |
| **Schalter EIN (normal)** | Relais EIN |
| **Neustart (bei Störung)** | Störung bleibt, LED blinkt |
| **Neustart (normal)** | Relais bleibt AUS |
| **Intervall aktiv** | Relais EIN für Intervalllänge, dann AUS bis nächstes Intervall |
| **Manuelles EIN/AUS** | Setzt Intervall-Countdown zurück |

## LED

| Zustand | Bedeutung |
|---------|-----------|
| **Dauerhaft blau** | Bereit / Sicherheitsfreigabe erteilt |
| **Blinkend blau** | Störung aktiv |
| **Rot** | Relais EIN (Hardware-verkabelt) |

## Einstellungen

| Entität | Beschreibung | Standard |
|---------|--------------|----------|
| Stromlimit | Auslöseschwelle | 10 A |
| Auslöseverzögerung | Zeit über Limit bis Auslösung | 200 ms |
| Intervall | Timer-Intervall (Aus, 1h-24h) | Aus |
| Intervalllänge | Laufzeit pro Zyklus | 5 min |

## Details

<details>
<summary><strong>Alle Entitäten</strong></summary>

**Steuerung**
- Schalter
- Sicherheitsfreigabe
- Zyklus nach Boot starten
- Intervall
- Intervalllänge
- Stromlimit
- Auslöseverzögerung

**Messwerte**
- Strom / Strom (Spitze)
- Spannung
- Leistung / Leistung (Schein) / Leistung (Blind) / Leistung (Faktor)
- Energie / Energie (Gesamt) / Energie (Tag)
- Zyklus (verbleibend) / Zyklus (seit letztem) / Zyklus (bis nächster)

**System**
- Status / Störung
- Betriebszeit / WLAN-Signal
- IP-Adresse / MAC-Adresse / Verbundenes WLAN
- ESPHome-Version
- Neustart / Werksreset / Sicherheitsmodus / Spitzenwert zurücksetzen

</details>

<details>
<summary><strong>Hardware</strong></summary>

Athom Smart Plug V3 (ESP32-C3)

| GPIO | Funktion |
|------|----------|
| 3 | Taste |
| 5 | Relais |
| 6 | Blaue LED |
| 20 | CSE7766 RX |

**Persistiert** (überlebt Neustart): Störung, Stromlimit, Auslöseverzögerung, Intervall, Intervalllänge, Zyklus nach Boot, Energie (Gesamt)

</details>
