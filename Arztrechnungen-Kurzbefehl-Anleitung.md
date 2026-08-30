# Kurzbefehl "Arztrechnungen scannen" – Bauanleitung

Ziel: Mehrere Arztrechnungen nacheinander mit dem iPhone scannen, jede als
eigene PDF in einem Ordner ablegen, am Ende alle PDFs teilen können und
gleichzeitig eine CSV-Tabelle führen (Datum, Arzt, Betrag, Kostenträger),
die du später in Numbers/Excel öffnen kannst.

Kurzbefehle ist dafür das richtige Tool. Ein paar Dinge kann die App nicht
direkt (z. B. live in eine Numbers-Datei schreiben) – dafür nutzen wir eine
CSV-Datei als Zwischenschritt, die Numbers klaglos öffnet.

---

## Vorbereitung (einmalig)

1. In der **Dateien**-App (am besten in iCloud Drive) einen Ordner anlegen,
   z. B. `Arztrechnungen`.
2. Darin eine leere Textdatei `Arztrechnungen.csv` anlegen mit der
   Kopfzeile (z. B. über die Notizen-App erstellen, dann in "Dateien"
   verschieben und in `.csv` umbenennen):
   ```
   Datum;Arzt;Betrag;Kostentraeger;Dateiname
   ```
   (Semikolon als Trenner, damit Numbers/Excel es in Deutschland sauber
   in Spalten aufteilt.)

---

## Aufbau des Kurzbefehls

Neuer Kurzbefehl → Name z. B. **"Arztrechnungen scannen"**.

### 1. Anzahl abfragen
- Aktion **"Eingabe abfragen"** (Ask for Input)
  - Eingabetyp: *Zahl*
  - Frage: „Wie viele Arztrechnungen möchtest du scannen?“
  - Variable merken als `Anzahl`

### 2. Zwei Listen-Variablen anlegen
- Aktion **"Liste"** → leer lassen → Variable setzen als `PDF-Liste`
- Aktion **"Liste"** → leer lassen → Variable setzen als `CSV-Zeilen`

### 3. Schleife: "Wiederholen" mit `Anzahl`
Innerhalb der Schleife (`Wiederholungsindex` steht automatisch zur
Verfügung):

**a) Dokument scannen**
- Aktion **"Dokumente scannen"** (Scan Document)
  - Damit fotografierst du alle Seiten *einer* Rechnung (Mehrseiten
    werden automatisch zu einer PDF zusammengefasst).

**b) Angaben zur Rechnung abfragen**
- **"Eingabe abfragen"** → Text → „Name des Arztes / der Praxis“ →
  Variable `Arzt`
- **"Eingabe abfragen"** → Zahl → „Rechnungsbetrag in €“ → Variable
  `Betrag`
- **"Eingabe abfragen"** → Datum → „Rechnungsdatum“ (Standard: aktuelles
  Datum) → Variable `Datum`
- **"Menü auswählen"** → Optionen: `Beihilfe`, `PKV`, `50/50 (beide)` →
  Variable `Kostentraeger`

**c) Datei sinnvoll benennen**
- Aktion **"Datum formatieren"** → Format `JJJJ-MM-TT` → Variable
  `Datum-formatiert`
- Aktion **"Text"** → Inhalt:
  `Datum-formatiert_Arzt.pdf` (z. B. `2026-08-30_Dr-Müller.pdf`)
  → Variable `Dateiname`
- Aktion **"Datei umbenennen"** auf das gescannte Dokument, neuer Name =
  `Dateiname`

**d) Datei ablegen**
- Aktion **"Datei sichern"** (Save File)
  - Ziel: der vorbereitete Ordner `Arztrechnungen`
  - „Bei Namenskonflikt“: *Neuen Namen erstellen* (damit nichts
    überschrieben wird)
- Aktion **"Zu Variable hinzufügen"** → gescanntes Dokument → zu
  `PDF-Liste` hinzufügen (für die spätere Sammel-Freigabe)

**e) CSV-Zeile bauen**
- Aktion **"Text"** → Inhalt:
  `Datum-formatiert;Arzt;Betrag;Kostentraeger;Dateiname`
- Aktion **"Zu Variable hinzufügen"** → diesen Text → zu `CSV-Zeilen`
  hinzufügen

*(Schleifenende)*

### 4. CSV-Datei aktualisieren
Nach der Schleife:

- Aktion **"Datei abrufen"** → Pfad direkt auf
  `Arztrechnungen/Arztrechnungen.csv` zeigen lassen (Dokumentenauswahl
  einmalig bestätigen, „Immer erlauben“ anhaken) → Variable
  `Bisheriger-Inhalt`
- Aktion **"Text kombinieren"** (Combine Text), Trennzeichen *Neue Zeile*:
  - Zeile 1: `Bisheriger-Inhalt`
  - danach: alle Elemente aus `CSV-Zeilen`
  → Variable `Neuer-CSV-Inhalt`
- Aktion **"Datei sichern"** → Inhalt `Neuer-CSV-Inhalt`, Ziel wieder
  `Arztrechnungen/Arztrechnungen.csv`, „Bei Namenskonflikt“: *Überschreiben*

### 5. Zum Schluss: Teilen anbieten
- Aktion **"Menü auswählen"**: „PDFs jetzt teilen?“ → Ja/Nein
  - Bei „Ja“: Aktion **"Für Freigabe"** (Share Sheet) mit `PDF-Liste`
    → dort kannst du AirDrop, Mail, WhatsApp, „In Dateien sichern“ etc.
    wählen (z. B. um sie zusätzlich beim Beihilfe- oder
    PKV-Hochlade-Portal anzuhängen).

---

## Nutzung im Alltag

- **Als Home-Bildschirm-Symbol** anlegen (Kurzbefehl → Teilen → „Zum
  Home-Bildschirm“), dann hast du ein eigenes App-Icon zum Scannen.
- Alternativ über die **Aktionstaste** (iPhone 15 Pro/16) oder das
  **Kurzbefehle-Widget** starten.
- Am Ende jeder "Saison" (z. B. quartalsweise) öffnest du
  `Arztrechnungen.csv` einfach in **Numbers** oder **Excel** – beide
  importieren `.csv` automatisch als Tabelle mit Spalten.

## Grenzen, die du kennen solltest

- Kurzbefehle kann auf dem iPhone **nicht** direkt Zeilen in eine
  bestehende **.numbers**-Datei schreiben (das geht nur über AppleScript
  am Mac). Die CSV-Route oben ist der zuverlässige Workaround.
- „Dokumente scannen“ fasst mehrere fotografierte **Seiten** automatisch
  zu einer PDF zusammen – die Schleife oben ist dafür da, mehrere
  **getrennte** Rechnungen (= mehrere PDFs) in einem Durchlauf zu
  erfassen.
- Trage am Rechnungsbetrag ruhig auch den Aufteilungsschlüssel ein
  (Beihilfe-Satz/PKV-Satz), falls du später nachvollziehen willst, was
  wo eingereicht wurde – dafür ggf. Menü/Eingabe in Schritt 3b erweitern.
