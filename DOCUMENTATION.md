# PORTAL – aktuelle Zustandsdokumentation

Diese Datei beschreibt ausschließlich den **aktuellen Stand** der Anwendung.

## 1) Zweck
`portal.mshta` ist ein lokales Windows-10-HTA-Tool ohne externe Dependencies. Es erzeugt dynamische Buttons aus `portaldb.txt` und führt pro Button entweder eine **Link**-Aktion (Datei/Ordner/App/URI öffnen) oder eine **Clipboard**-Aktion aus.

## 2) Dateien
- `portal.mshta`  
  Enthält UI, Theme-System, Parsing, Dateizugriff und Aktionen.
- `portaldb.txt`  
  Enthält die Einträge in einem simplen zeilenbasierten Format.
- `plan.md`  
  Ursprünglicher Umsetzungsplan.

## 3) Datenformat in `portaldb.txt`
Standardzeile:

`Name|Value`

Beispiele:
- `Notepad|C:\Windows\System32\notepad.exe` (wird als **Link** behandelt)
- `Hallo|"Hello World"` (wird als **Clipboard** behandelt)

Regeln:
- Leere Zeilen und `#`-Kommentare werden ignoriert.
- Legacy-Format `Name|Type|Value` wird weiterhin gelesen:
  - `Clipboard` bleibt Clipboard.
  - `LocalLink` und alle anderen Typen werden auf Link abgebildet.

## 4) Automatische Typ-Erkennung
Der Benutzer wählt **keine Kategorie** im Formular.

Erkennung erfolgt über `Value`:
- Wert ist in einfachen oder doppelten Anführungszeichen -> `Clipboard`
- Sonst -> `Link`

Damit entsteht ein sehr simples Eingabeprinzip:
- Ziel ohne Quotes = Öffnen
- Text mit Quotes = Kopieren

## 5) UI-Aufbau
Die Oberfläche hat zwei Karten:

1. **Toolbar-Karte oben**
   - Titel
   - Theme-Wechsel-Button
   - Eingabe `Name`
   - Eingabe `Value`
   - `Hinzufügen`-Button
   - Statuszeile

2. **Content-Karte**
   - zentrierter PORTAL-Titel
   - kurzer Untertitel für Nutzungsregeln
   - dynamisch generierte Launcher-Buttons
   - kleine Hinweiszeile mit Beispielen

## 6) Theme-System
Es gibt mehrere sofort nutzbare Themes und einen Button zum Durchschalten:
- Hell
- Dunkel
- Forest
- Violet

Technik:
- Theme-Werte werden als CSS-Variablen (`--bg`, `--panel`, `--button`, …) gehalten.
- `applyTheme(index)` setzt diese Variablen auf `document.documentElement`.
- `nextTheme()` rotiert zyklisch durch das Theme-Array.

## 7) Start- und Datenfluss
`initApp()`:
1. setzt Fenstergröße
2. initialisiert `Scripting.FileSystemObject` + `WScript.Shell`
3. berechnet den Pfad der nebenliegenden `portaldb.txt`
4. erzeugt die Datei bei Bedarf
5. lädt Theme 0 (Hell)
6. lädt und rendert alle Buttons

`loadEntries()`:
1. liest alle Zeilen
2. parst jede Zeile mit `parseLine()`
3. erzeugt pro validem Datensatz einen Button
4. bindet Klick auf `runAction(type, value)`

## 8) Aktionen
### Link
`openLink(target)` versucht zuerst direkt `shell.Run("<target>")`.
Wenn das fehlschlägt, folgt Fallback auf Explorer-Aufruf.

Dadurch funktionieren mit derselben Kategorie:
- Ordnerpfade
- Dateipfade
- EXE-Apps
- unterstützte URI-/Protokollziele

### Clipboard
`copyToClipboard(text)` nutzt `window.clipboardData`; falls nötig, Fallback via `htmlfile`-ActiveX.

## 9) Formular-Verhalten
`addEntry()`:
- validiert Name + Value
- bestimmt Typ automatisch via `detectTypeFromValue`
- speichert als `Name|Value` in `portaldb.txt`
- leert Inputs
- lädt Buttonliste neu
- zeigt Statusmeldung inkl. erkannten Typ

## 10) Technische Grenzen (aktueller Stand)
- Fokus auf MSHTA/Windows-Umgebung mit ActiveX.
- Keine Imports, keine externen Libraries.
- Kein Netz- oder Serverzwang, alles lokal dateibasiert.
