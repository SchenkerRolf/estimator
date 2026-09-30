# Schätzrunde

Ein schlankes Schätz-Tool für Backlog Refinements und Sprint Plannings. Kein Login, keine Paywall, und es wird nichts gespeichert.

## Funktionen
- Die Moderation startet eine Session und teilt den Link oder den dreistelligen Code (z. B. 427). Alle anderen geben nur ihren Namen ein.
- Skala: **0.5 · 1 · 2 · 3 · 5 · ? · ☕**
- Rollen: Moderation (schätzt wahlweise mit), Schätzende und Zuschauende (z. B. PO)
- Die Karten werden automatisch aufgedeckt, sobald alle gewählt haben. Die Moderation kann auch früher aufdecken.
- Bei Abweichungen werden der tiefste und der höchste Wert mit Namen hervorgehoben. Danach folgt eine neue Runde.
- Nach dem Aufdecken zeigt ein Kreisdiagramm die Verteilung der Stimmen.
- Kein Backlog: Ihr besprecht das Item im Ticketsystem. Die Moderation startet mit «Neue Runde» bzw. «Neu starten» die nächste Abstimmung.

## So funktioniert es technisch
Das Tool ist eine einzige Datei `index.html`. Die Echtzeit-Verbindung läuft über **Firebase Realtime Database** (Standort Belgien) mit **anonymer Anmeldung**. Für die Nutzer:innen gibt es kein Login. Alles läuft über HTTPS (Port 443).

- Gespeichert werden nur der Session-Code, die eingegebenen Namen, die Rollen, der Online-Status und die Stimmen der **aktuellen** Runde.
- «Session beenden» löscht alles sofort. Vergessene Sessions werden nach 12 Stunden ohne Änderung automatisch entfernt, sobald jemand eine neue Session startet.
- Die Sicherheitsregeln (`database.rules.json`) sorgen dafür, dass:
  - Stimmen erst nach dem Aufdecken lesbar sind,
  - jede Person nur ihre eigene Stimme setzen kann,
  - nur die Moderation neue Runden startet oder Personen entfernt.
- Schliesst die Moderation den Tab, läuft die Session weiter. Die anderen können die Moderation übernehmen.
- Die Firebase-Konfiguration in `index.html` ist kein Geheimnis. Geschützt wird der Zugriff durch die Regeln.

### Einrichtung in Firebase
1. Unter *Realtime Database → Regeln* den Inhalt von `database.rules.json` einfügen und auf **Veröffentlichen** klicken.
2. Unter *Authentication → Anmeldemethode* muss **Anonym** aktiviert sein.
3. Optional: In der Google Cloud Console den API-Key auf die eigene Domain beschränken (HTTP-Referrer).

### Designsystem
Die Oberfläche nutzt das Designsystem der Stadt Zürich: `@oiz/stzh-components` 4.16.0, geladen über jsDelivr.
- **Komponenten:** Buttons, Eingabefelder, Radiogroup, Toggle, Status, Meldungen, Toasts, Dialog und Loader kommen aus dem Designsystem.
- **Eigene Elemente:** Die Schätzkarten und das Kreisdiagramm sind eigene Elemente. Sie verwenden aber ausschliesslich die Farb-, Schrift- und Abstands-Tokens des Systems.
- **Kreisdiagramm:** Die Farben kommen aus der Rampe midnightblue 40–80, «?» und «☕» aus coolgrey.
- **Kein Dunkelmodus:** Das Designsystem bietet keinen.

### Netzwerk
Die Browser müssen diese Adressen erreichen können:
- `cdn.jsdelivr.net`
- `*.firebasedatabase.app`
- `identitytoolkit.googleapis.com`
- `securetoken.googleapis.com`
- `www.gstatic.com`

## Optionen
- `?transport=local`: Testmodus ohne Firebase. Er funktioniert nur zwischen Tabs im selben Browser.
