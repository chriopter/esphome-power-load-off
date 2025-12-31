# Strombegrenzer

ESPHome für Athom Smart Plug V3 mit Überstromschutz und Intervall-Timer. Bei Überstrom schaltet das Relais ab und geht in Störung. Mit Intervall bleibt das Relais nach Einschalten nur für die eingestellte Zeit an.

## Status

| Zustand | LED |
|---------|-----|
| Störung | blinkt blau |
| Relais aus | dauerhaft blau |
| Relais an | rot |

## Taste am Gerät

| Zustand | Aktion | Ergebnis |
|---------|--------|----------|
| Störung | kurz drücken | Störung zurücksetzen* |
| Relais aus | kurz drücken | Relais einschalten |
| Relais an | kurz drücken | Relais ausschalten |
| beliebig | 4 Sek. halten | Werksreset |

*Zum Starten nach Störung: zweimal drücken

## Einstellungen

| Entität | Standard |
|---------|----------|
| Stromlimit | 10 A |
| Auslöseverzögerung | 200 ms |
| Intervall | Aus |
| Intervalllänge | 5 min |

## Details

<details>
<summary><strong>Entitäten</strong></summary>

- Schalter, Sicherheitsfreigabe
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

**Persistiert:** Störung, Stromlimit, Auslöseverzögerung, Intervall, Intervalllänge, Energie (Gesamt)

</details>
