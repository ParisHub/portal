# PORTAL – aktuelle Zustandsdokumentation (Version 1)

Diese Doku beschreibt **nur den aktuellen Stand** im Repository.

## Überblick
`portal.mshta` ist eine kleine, lokale Windows-10-HTA-Anwendung ohne externe Abhängigkeiten. Sie liest eine Datei `portaldb.txt` aus dem gleichen Ordner, erzeugt daraus Buttons und führt pro Button eine Aktion aus.

## Dateien
- `portal.mshta`  
  Enthält UI, Parsing, Dateizugriffe und Aktionslogik.
- `portaldb.txt`  
  Enthält die konfigurierten Einträge im zeilenbasierten Codex-Format.
- `plan.md`  
  Beschreibt die Zielanforderungen und das gewünschte Verhalten.

## `portaldb.txt`-Format
Jede aktive Zeile hat dieses Schema:

`Name|Type|Value`

- `Name`: Beschriftung des Buttons.
- `Type`: Aktionstyp (`LocalLink` oder `Clipboard`).
- `Value`: Parameter für die Aktion.

Ergänzende Regeln:
- Leere Zeilen werden ignoriert.
- Zeilen, die mit `#` beginnen, werden als Kommentar ignoriert.
- Wenn in `Value` das Zeichen `|` vorkommt, wird es unterstützt (alles ab dem 3. Feld zählt als Wert).

## UI-Struktur von `portal.mshta`
Die Anwendung besteht aus zwei Blöcken:

1. **Kopfbereich (Editor)**
   - Eingabe `Name`
   - Auswahl `Type` (`LocalLink`, `Clipboard`)
   - Eingabe `Value`
   - Button `Hinzufügen`
   - Statuszeile für Erfolg/Fehler

2. **Inhaltsbereich**
   - Zentrale Überschrift `PORTAL`
   - Darunter dynamisch erzeugte Aktions-Buttons aus `portaldb.txt`

## Laufzeitablauf
Beim Start (`initApp`) passiert:
1. Fenstergröße wird auf ein kleines Format gesetzt.
2. `Scripting.FileSystemObject` und `WScript.Shell` werden erzeugt.
3. Pfad zur `portaldb.txt` wird aus dem Speicherort der `.mshta` gebildet.
4. Wenn `portaldb.txt` fehlt, wird sie mit Kopfzeile erstellt.
5. Alle Einträge werden geladen und als Buttons gerendert.

## Parsing- und Renderinglogik
- Jede Zeile wird mit `parseLine` in `name`, `type`, `value` aufgeteilt.
- Nur valide Datensätze erzeugen Buttons.
- Jeder Button trägt intern `data-type` und `data-value`.
- Klick auf einen Button ruft `runAction(type, value)` auf.

## Unterstützte Aktionen
### `LocalLink`
- Öffnet per `WScript.Shell.Run` den Windows Explorer für den angegebenen Pfad.

### `Clipboard`
- Schreibt den angegebenen Text in die Zwischenablage.
- Nutzt `window.clipboardData` oder als Fallback ein `htmlfile`-ActiveX-Objekt.

## Schreiben neuer Einträge
`addEntry` hängt einen neuen Datensatz direkt an `portaldb.txt` an:
- Validierung: `Name`, `Type`, `Value` müssen gesetzt sein.
- Speicherung als eine Zeile: `Name|Type|Value`
- Nach erfolgreichem Schreiben wird die Liste neu geladen.

## Technische Leitplanken im aktuellen Stand
- Keine externen Libraries, keine Paketabhängigkeiten.
- Nur HTA/JavaScript/ActiveX, wie auf Windows 10 verfügbar.
- Alles ist lokal dateibasiert (keine Netzwerkabhängigkeit).
