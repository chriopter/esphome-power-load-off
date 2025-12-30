# Strombegrenzer

ESPHome für Athom Smart Plug V3 mit Strombegrenzung und Intervall-Timer.

## Verhalten

| Aktion | LED | Ergebnis |
|--------|-----|----------|
| **Strom > Limit für Auslöseverzögerung** | Blinkt | Relais AUS, Störung aktiv |
| **Taste kurz bei Störung** | Dauerhaft | Störung zurückgesetzt |
| **Taste kurz normal** | — | Relais toggeln |
| **Taste lang 4s** | — | Werksreset |
| **Schalter EIN bei Störung** | Blinkt | Blockiert |
| **Schalter EIN normal** | Dauerhaft | Relais EIN |
| **Neustart bei Störung** | Blinkt | Störung bleibt |
| **Neustart normal** | Dauerhaft | Relais bleibt AUS |

### Zeitschaltuhr

| Aktion | LED | Ergebnis |
|--------|-----|----------|
| **Intervall erreicht** | Dauerhaft | Relais EIN für Intervalllänge |
| **Intervalllänge abgelaufen** | Dauerhaft | Relais AUS, wartet auf nächstes Intervall |
| **Manuelles EIN/AUS** | — | Intervall-Countdown startet neu |
| **Störung während Zyklus** | Blinkt | Zyklus stoppt, wartet auf Reset |
| **Zyklus nach Boot = AN** | Dauerhaft | Startet sofort nach Boot |
| **Zyklus nach Boot = AUS** | Dauerhaft | Wartet auf erstes Intervall |

### LED

| LED | Bedeutung |
|-----|-----------|
| Blau dauerhaft | Bereit |
| Blau blinkend | Störung |
| Rot | Relais EIN (hardwired) |

## Einstellungen

| Entität | Beschreibung | Standard |
|---------|--------------|----------|
| Stromlimit | Schwelle in Ampere | 10 A |
| Auslöseverzögerung | Zeit über Limit bis Abschaltung | 200 ms |
| Intervall | Aus / 1h / 2h / 3h / 4h / 6h / 8h / 12h / 24h | Aus |
| Intervalllänge | Laufzeit pro Zyklus (1-30 min) | 5 min |

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

**Persistiert:** Störung, Stromlimit, Auslöseverzögerung, Intervall, Intervalllänge, Zyklus nach Boot, Energie (Gesamt)

</details>
