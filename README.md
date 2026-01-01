# Strombegrenzer

ESPHome für Athom Smart Plug V3 mit Überstromschutz und Intervall-Timer.

- **Überstromschutz:** Bei Überstrom schaltet das Relais ab und geht in Störung.
- **Intervalllänge:** Relais schaltet nach dieser Zeit automatisch ab.
- **Intervall (optional):** Startet Relais automatisch in diesem Abstand. Stunden-Intervalle starten zur vollen Stunde (z.B. 9:46 + 2h → 12:00), Minuten-Intervalle sofort. Manuelles Schalten beeinflusst den Zeitplan nicht. Ohne WiFi per Timer statt Uhrzeit.

## Status

| Zustand | LED |
|---------|-----|
| Störung | blinkt blau |
| Relais aus | dauerhaft blau |
| Relais an | rot |

## Taste am Gerät

| Zustand | LED | Aktion | Ergebnis |
|---------|-----|--------|----------|
| Störung | blinkt blau | kurz drücken | Störung zurücksetzen* |
| Relais aus | dauerhaft blau | kurz drücken | Zyklus starten** |
| Relais an | rot | kurz drücken | Relais ausschalten |
| beliebig | - | 4 Sek. halten | Werksreset |

*Zum Starten nach Störung: zweimal drücken

**Relais an für Intervalllänge

## Nach Stromausfall / Neustart

Relais startet aus, Störung ist aktiv (Sicherheitsfreigabe erforderlich). Einstellungen bleiben erhalten, Zeitplan wird neu berechnet (jetzt + Intervall).

## Sicherheitsfreigabe

Der Zeitplan läuft während einer Störung weiter (verpasste Zyklen werden übersprungen). Nach Freigabe startet der nächste reguläre Zyklus.

Beispiel (1h Intervall): Störung um 12:01 → 12:00 verpasst, Zeitplan springt auf 13:00. Freigabe um 12:30 → nächster Start 13:00.

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
- Zyklus (verbleibend), Zyklus (letzter), Zyklus (nächster), Zyklus (nächster, Countdown)
- Status, Störung, Betriebszeit, WLAN-Signal
- IP-Adresse, MAC-Adresse, Verbundenes WLAN, ESPHome-Version
- Neustart, Werksreset, Sicherheitsmodus, Spitzenwert zurücksetzen

</details>

<details>
<summary><strong>Hardware</strong></summary>

Athom Smart Plug V3 (ESP32-C3): GPIO3=Taste, GPIO5=Relais, GPIO6=LED, GPIO20=CSE7766

**Persistiert:** Stromlimit, Stromlimit (Verzögerung), Intervall, Intervalllänge, Energie (Gesamt)

</details>

