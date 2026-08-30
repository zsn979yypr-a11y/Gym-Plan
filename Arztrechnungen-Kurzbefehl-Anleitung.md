# Kurzbefehl "Arztrechnungen scannen" – Bauanleitung (geprüfte Version)

Ziel: Mehrere Arztrechnungen nacheinander mit dem iPhone scannen, jede als
eigene PDF in einem Ordner ablegen, am Ende alle PDFs teilen können und
gleichzeitig eine CSV-Tabelle führen (Datum, Arzt, Betrag, Kostenträger),
die du später in Numbers/Excel öffnen kannst.

> **Hinweis zur Recherche:** Die exakten Aktionsnamen unten wurden über
> Websuche gegen mehrere unabhängige Quellen geprüft (siehe „Quellen“ am
> Ende). Apples offizielle Support-Seiten (support.apple.com) waren aus
> dieser Arbeitsumgebung leider nicht direkt abrufbar (vom Netzwerk
> blockiert) – die Angaben stammen daher aus Community-Quellen und
> Blogbeiträgen, die die Original-Aktionsnamen zitieren. Kleinere
> Abweichungen durch iOS-Versionsunterschiede sind möglich; wo ich mir
> nicht sicher war, steht das explizit dabei.

---

## Wichtige Korrektur gegenüber der ersten Version

Die Aktion **"Dokument scannen"** übergibt das Scan-Ergebnis in der Praxis
**nicht zuverlässig als direkt weiterverarbeitbare Variable** an die
nächste Aktion – das ist eine bekannte, mehrfach dokumentierte
Einschränkung. Stattdessen legt sie die Datei automatisch im **zuletzt
benutzten Ordner** ab.

Der zuverlässige, rein native Workaround: Wir sorgen dafür, dass der
"zuletzt benutzte Ordner" immer ein fester Puffer-Ordner ist, lassen dort
scannen, und holen die Dateien danach ganz regulär mit **"Ordnerinhalt
laden"** wieder ab. Kein Kauf einer Zusatz-App nötig.

---

## Vorbereitung (einmalig)

1. **Dateien-App** → in iCloud Drive einen Ordner `Arztrechnungen` anlegen.
2. Darin einen Unterordner `_ScanPuffer` anlegen. Der bleibt zwischen den
   Läufen leer – er ist nur eine Zwischenstation.
3. **Puffer als „zuletzt benutzten Ordner" festlegen (einmalig, wichtig):**
   In der Dateien-App in den Ordner `_ScanPuffer` wechseln → oben rechts
   auf die drei Punkte → **„Dokumente scannen"** → eine beliebige Seite
   scannen (z. B. ein Blatt Papier) und dort speichern. Diese eine Test-Datei
   danach wieder löschen. Damit „merkt" sich iOS, dass zukünftige Scans
   erstmal in `_ScanPuffer` landen.
4. Die schon fertige `Arztrechnungen.csv` (mit Kopfzeile
   `Datum;Arzt;Betrag;Kostentraeger;Dateiname`) in den Ordner
   `Arztrechnungen` legen (nicht in den Puffer-Unterordner).

---

## Aufbau des Kurzbefehls

Neuer Kurzbefehl → Name z. B. **„Arztrechnungen scannen"**.

### 1. Anzahl abfragen
- Aktion **„Eingabe abfragen"** (Ask for Input)
  - Eingabetyp: *Zahl*
  - Frage: „Wie viele Arztrechnungen möchtest du scannen?"
  - Variable merken als `Anzahl`

### 2. Leere Liste für die CSV-Zeilen anlegen
- Aktion **„Liste"** → leer lassen → Variable setzen als `CSV-Zeilen`

### 3. Scan-Phase – Schleife „Wiederholen" mit `Anzahl`
Bewusst denkbar einfach gehalten – nur der Scan-Schritt, keine
Unterbrechung:

- Aktion **„Dokument scannen"** (im Aktionen-Suchfeld „scan" eingeben,
  falls die Aktion nicht sofort mit diesem Namen auftaucht)
  - Seiten einer Rechnung fotografieren, dann auf **„Sichern"** tippen.
    Das ist dein „Rechnung fertig"-Button. Die Datei landet automatisch
    in `_ScanPuffer` (siehe Vorbereitung, Schritt 3).

*(Schleifenende)* → Scanner öffnet sich beim nächsten Durchlauf
automatisch erneut, bis `Anzahl` erreicht ist.

### 4. Gescannte Dateien aus dem Puffer holen
Nach der Schleife:

- Aktion **„Ordnerinhalt laden"** (Get Folder Contents) → Ordner
  `_ScanPuffer` → Sortierung: *Erstellungsdatum*, Reihenfolge: *älteste
  zuerst* (damit die Reihenfolge deinem Scan-Ablauf entspricht) →
  Variable `PDF-Liste`

### 5. Beschriften-Phase – „Wiederholen mit jedem Element" über `PDF-Liste`
(Im Aktionen-Suchfeld „wiederholen" eingeben, die Variante für Listen
wählen – manche iOS-Versionen nennen sie „Wiederholen mit jedem
Element", andere „Für jedes Element wiederholen".) Element-Variable z. B.
`Aktuelles-PDF`:

**a) Angaben zur Rechnung abfragen**
- **„Eingabe abfragen"** → Text → „Name des Arztes / der Praxis" →
  Variable `Arzt`
- **„Eingabe abfragen"** → Zahl → „Rechnungsbetrag in €" → Variable
  `Betrag`
- **„Eingabe abfragen"** → Datum → „Rechnungsdatum" (Standard: aktuelles
  Datum) → Variable `Datum`
- **„Menü auswählen"** → Optionen: `Beihilfe`, `PKV`, `50/50 (beide)` →
  Variable `Kostentraeger`

**b) Datei sinnvoll benennen**
- Aktion **„Datum formatieren"** → Eingabe: `Datum` → im Format-Dropdown
  **„ISO 8601"** auswählen (nicht „Benutzerdefiniert" – dort müsste man
  sonst einen technischen Code selbst richtig eintippen) → liefert
  automatisch `2026-08-30`-Schreibweise → Variable `Datum-formatiert`
- Aktion **„Text"** → Inhalt: `Datum-formatiert_Arzt.pdf` (z. B.
  `2026-08-30_Dr-Müller.pdf`) → Variable `Dateiname`
- Aktion **„Datei umbenennen"** auf `Aktuelles-PDF`, neuer Name =
  `Dateiname`

**c) Datei aus dem Puffer in den richtigen Ordner verschieben**
- Aktion **„Datei in Ordner bewegen"** (Move File) → `Aktuelles-PDF` →
  Ziel: `Arztrechnungen` (der Hauptordner, **nicht** `_ScanPuffer`)
  - „Bei Namenskonflikt": *Neuen Namen erstellen*
  - Wichtig: *Bewegen*, nicht kopieren – so ist `_ScanPuffer` danach
    automatisch wieder leer für den nächsten Lauf.

**d) CSV-Zeile bauen**
- Aktion **„Text"** → Inhalt:
  `Datum-formatiert;Arzt;Betrag;Kostentraeger;Dateiname`
- Aktion **„Zu Variable hinzufügen"** → diesen Text → zu `CSV-Zeilen`
  hinzufügen

*(Schleifenende)*

### 6. CSV-Datei aktualisieren
- Aktion **„Datei abrufen"** → Pfad direkt auf
  `Arztrechnungen/Arztrechnungen.csv` zeigen lassen (einmalig
  Dokumentenauswahl bestätigen, „Immer erlauben" anhaken) → Variable
  `Bisheriger-Inhalt`
- Aktion **„Text kombinieren"** (Combine Text), Trennzeichen *Neue Zeile*:
  - Zeile 1: `Bisheriger-Inhalt`
  - danach: alle Elemente aus `CSV-Zeilen`
  → Variable `Neuer-CSV-Inhalt`
- Aktion **„Datei sichern"** → Inhalt `Neuer-CSV-Inhalt`, Ziel wieder
  `Arztrechnungen/Arztrechnungen.csv`, „Bei Namenskonflikt": *Überschreiben*

### 7. Zum Schluss: Teilen anbieten
- Aktion **„Menü auswählen"**: „PDFs jetzt teilen?" → Ja/Nein
  - Bei „Ja": Aktion **„Ordnerinhalt laden"** erneut auf `Arztrechnungen`
    (Hauptordner) anwenden, gefiltert/sortiert nach den heute erstellten
    Dateien, dann **„Für Freigabe"** (Share Sheet) damit → AirDrop, Mail,
    „In Dateien sichern" etc.
  - *(Einfacher, falls dir das zu kompliziert ist: lass diesen Schritt
    weg und teile die fertigen PDFs danach ganz normal über die
    Dateien-App per Mehrfachauswahl.)*

---

## Nutzung im Alltag

- **Als Home-Bildschirm-Symbol** anlegen (Kurzbefehl → Teilen → „Zum
  Home-Bildschirm").
- Alternativ über die **Aktionstaste** (iPhone 15 Pro/16) oder das
  **Kurzbefehle-Widget** starten.
- `Arztrechnungen.csv` bei Bedarf einfach in **Numbers** oder **Excel**
  öffnen – beide importieren `.csv` automatisch als Tabelle.

## Grenzen, die du kennen solltest

- Die **„Dokument scannen"-Aktion** kann ihr Ergebnis nicht zuverlässig
  direkt als Variable weiterreichen – deshalb der Umweg über den
  Puffer-Ordner + „Ordnerinhalt laden". Das ist keine Bastel-Lösung von
  mir, sondern der in der Community dokumentierte Standard-Workaround.
- Kurzbefehle kann auf dem iPhone **nicht** direkt Zeilen in eine
  bestehende `.numbers`-Datei schreiben (nur über AppleScript am Mac).
  Die CSV-Route ist der zuverlässige Workaround.
- Exakte Bezeichnungen einzelner Aktionen (z. B. „Wiederholen mit jedem
  Element" vs. „Für jedes Element wiederholen", oder „Ordnerinhalt
  laden" vs. „Ordnerinhalt abrufen") können sich zwischen iOS-Versionen
  leicht unterscheiden. Nutze im Zweifel die Suche im
  Aktionen-Auswahlbildschirm mit einem Stichwort (z. B. „wiederholen",
  „ordner", „scan") – die passende Aktion taucht dann auf, auch wenn der
  Name nicht 1:1 passt.
- Trage am Rechnungsbetrag ruhig auch den Aufteilungsschlüssel ein
  (Beihilfe-Satz/PKV-Satz), falls du später nachvollziehen willst, was
  wo eingereicht wurde – dafür ggf. Menü/Eingabe in Schritt 5a erweitern.

## Quellen

- [Kurzbefehl Dokument scannen – Apfeltalk-Forum](https://www.apfeltalk.de/community/threads/kurzbefehl-dokument-scannen.572783/) –
  bestätigt die Einschränkung der „Dokument scannen"-Aktion und den
  „zuletzt benutzter Ordner"-Effekt.
- [Interaktiver Quittungs-Scanner per Kurzbefehle-App – mac-seminare Blog](https://mac-seminare.de/blog/2020/04/26/interaktiver-quittungs-scanner-per-kurzbefehle-app-ios-ipados/) –
  gleiche Einschränkung, mit Praxisbeispiel für Quittungen/Rechnungen.
- [Simple Scan Aped My Shortcut – HeyDingus (August 2024)](https://heydingus.net/blog/2024/8/simple-scan-aped-my-shortcut) –
  bestätigt, dass die Einschränkung auch in aktuelleren iOS-Versionen
  weiterhin besteht.
- [Dateien organisieren per Kurzbefehl – mac-seminare Blog](https://mac-seminare.de/blog/2022/07/13/dateien-organisieren-per-kurzbefehl/) –
  Beleg für die Aktionen „Ordnerinhalt laden" und „Datei in Ordner
  bewegen".
- [Custom date formats in Shortcuts – Apple Support](https://support.apple.com/guide/shortcuts/custom-date-formats-apd8d9b19184/ios) –
  belegt, dass eigene Formatcodes kleingeschrieben nach dem
  Unicode-Standard funktionieren (`yyyy-MM-dd`, nicht `JJJJ-MM-TT`) –
  Grund, warum wir stattdessen den fertigen „ISO 8601"-Eintrag im
  Dropdown nutzen, um dieses Risiko ganz zu vermeiden.

## Was in dieser Anleitung wirklich geprüft ist – und was nicht

- **Mehrfach durch unabhängige Quellen bestätigt:** die Einschränkung der
  „Dokument scannen"-Aktion, die Aktionen „Ordnerinhalt laden" und
  „Datei in Ordner bewegen", der grundsätzliche Bedienablauf (Aktion
  suchen/hinzufügen, Variable umbenennen), das ISO-8601-Datumsformat.
- **Nicht unabhängig verifiziert** (Apples eigene Support-Seiten waren
  aus der Arbeitsumgebung, in der diese Anleitung erstellt wurde, nicht
  erreichbar – Netzwerk blockiert): der exakte Name der
  Schleifen-Element-Variable (vermutlich „Wiederholungselement"), ob
  bestimmte Suchbegriffe die passende Aktion sofort ganz oben anzeigen,
  exakte Feldbezeichnungen bei „Menü auswählen". Diese Stellen bitte
  beim Nachbauen mit Vorsicht behandeln und im Zweifel im
  Aktionen-Suchfeld mit einem anderen Stichwort erneut suchen.
