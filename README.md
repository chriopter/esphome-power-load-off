# Strombegrenzer

ESPHome für Athom Smart Plug V3 mit Überstromschutz und Intervall-Timer.

- **Überstromschutz:** Bei Überstrom schaltet das Relais ab und geht in Störung.
- **Intervalllänge:** Relais schaltet nach dieser Zeit automatisch ab.
- **Intervall (optional):** Startet Relais automatisch in diesem Abstand.

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
| Relais aus | kurz drücken | Zyklus starten** |
| Relais an | kurz drücken | Relais ausschalten |
| beliebig | 4 Sek. halten | Werksreset |

*Zum Starten nach Störung: zweimal drücken

**Relais an für Intervalllänge

## Nach Stromausfall / Neustart

Relais startet immer aus. Störung und Einstellungen bleiben erhalten. Erster Zyklus startet nach Intervall-Abstand.

## Einstellungen

| Entität | Standard |
|---------|----------|
| Stromlimit | 10 A |
| Stromlimit (Verzögerung) | 200 ms |
| Intervall | Aus |
| Intervalllänge | 5 min |

## Details

<details>
<summary><strong>Entitäten</strong></summary>

- Schalter, Sicherheitsfreigabe
- Intervall, Intervalllänge, Stromlimit, Stromlimit (Verzögerung)
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

**Persistiert:** Störung, Stromlimit, Stromlimit (Verzögerung), Intervall, Intervalllänge, Energie (Gesamt)

</details>
