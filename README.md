# Gym-Plan – Krafttraining Tracker

Eine einfache, dunkle Web-App zum Tracken deines Krafttrainings (Upper A / Lower A / Upper B / Lower B). Kein Login, kein Server, keine Installation.

## Öffnen

Es gibt nur eine Datei: `index.html`. Zwei Möglichkeiten:

1. **Direkt öffnen:** Diese Datei aufs Handy oder den Computer laden und im Browser öffnen (z. B. per Mail/AirDrop an dich selbst schicken und dann öffnen).
2. **Über GitHub Pages hosten** (empfohlen fürs Handy, damit du eine feste Adresse hast):
   - In den Repo-Einstellungen auf GitHub unter „Pages" die Quelle auf den Branch mit `index.html` stellen.
   - Danach ist die App unter der von GitHub angezeigten `github.io`-Adresse erreichbar.
   - Auf dem iPhone: Seite in Safari öffnen → Teilen-Symbol → „Zum Home-Bildschirm" für ein App-Icon.

## Daten & Speicherung

- Alle Trainingsdaten werden im **lokalen Speicher deines Browsers** (`localStorage`) auf dem jeweiligen Gerät gespeichert – dauerhaft, ohne Server, ohne Cloud.
- Jedes abgeschlossene Training wird als eigener Eintrag mit Schlüssel `training:YYYY-MM-DD:tag-name` gespeichert (z. B. `training:2026-08-30:upper-a`).
- Wichtig: Die Daten sind an **diesen Browser auf diesem Gerät** gebunden. Wenn du den Browser-Cache/App-Daten löschst oder ein anderes Gerät nutzt, sind die alten Daten dort nicht sichtbar. Nutze regelmäßig den „Exportieren"-Button auf der Startseite, um dir eine CSV-Sicherung zu erstellen.

## Funktionen

- Startbildschirm mit vier Trainingstagen
- Feste Übungslisten pro Tag (Ziel-Sätze × Ziel-Wiederholungen)
- Eingabefelder für Gewicht (kg) und Wiederholungen pro Satz, mit Ziffern-Tastenfeld auf dem Handy
- Automatische Vorbefüllung des Gewichts mit dem Wert aus dem letzten Training dieser Übung
- Verlaufsansicht pro Übung (Datum + alle Sätze, neuestes zuerst) – Übungsname antippen, um sie zu sehen
- „Training abschließen" speichert alle ausgefüllten Sätze mit dem heutigen Datum
- „Exportieren"-Button auf der Startseite lädt eine `trainingsdaten-export.csv` herunter (öffnet sich direkt in Numbers/Excel)
