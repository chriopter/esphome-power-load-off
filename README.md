# Strombegrenzer

ESPHome für Athom Smart Plug V3 mit Strombegrenzung und Intervall-Timer.

## Status

| Zustand | LED |
|---------|-----|
| Störung | blinkt blau |
| Aus | dauerhaft blau |
| An | rot |

## Taste am Gerät

| Zustand | Aktion | Ergebnis |
|---------|--------|----------|
| Störung | kurz drücken | Störung zurücksetzen |
| Aus | kurz drücken | Gerät einschalten |
| An | kurz drücken | Gerät ausschalten |
| beliebig | 4 Sek. halten | Werksreset |

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
