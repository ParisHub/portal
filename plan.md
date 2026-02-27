# Plan für `portal.hta`

## 1) Ziel
Es soll eine kleine, eigenständige `portal.hta`-Anwendung für Windows 10 entstehen. Die App liest Konfigurationen aus einer `portaldb.txt`, die im **gleichen Ordner** liegt, und erzeugt daraus dynamische Buttons.

## 2) Technische Rahmenbedingungen
- Nur Standard-Mittel von HTA/Windows 10 verwenden.
- Keine Imports, keine externen Bibliotheken, keine zusätzlichen Abhängigkeiten.
- Falls Hilfslogik benötigt wird, muss sie direkt im eigenen Code enthalten sein.

## 3) Dateien
- `portal.hta` (UI + Logik)
- `portaldb.txt` (Datenbasis/Konfiguration)

Beide Dateien liegen nebeneinander im selben Verzeichnis.

## 4) Basis-UI
- Beim Start zeigt die Anwendung ein kleines Fenster.
- In der Mitte steht zunächst ein zentrierter Titel, z. B. `PORTAL`.
- Danach werden aus `portaldb.txt` zusätzliche Buttons geladen.

## 5) Datenformat (`portaldb.txt`)
Einfaches zeilenbasiertes Codex-Format mit `|` als Trennzeichen:

```txt
Name|LocalLink|LF:/Data/Fin/
Name|Clipboard|"Hello World"
```

Bedeutung pro Zeile:
1. **Name**: Beschriftung des Buttons in der UI.
2. **Typ**: Aktionstyp, z. B. `LocalLink` oder `Clipboard`.
3. **Wert**: Parameter für die Aktion (Pfad oder Text).

## 6) Laufzeitverhalten
- `portal.hta` prüft beim Start, ob `portaldb.txt` vorhanden ist.
- Wenn Einträge vorhanden sind, wird jede Zeile nach dem Codex geparst.
- Für jeden gültigen Eintrag wird ein Button mit dem jeweiligen Namen erstellt.
- Ungültige oder leere Zeilen werden sauber ignoriert (ohne Absturz).

## 7) Aktionen
### `LocalLink`
- Klick auf den Button öffnet den angegebenen lokalen Pfad im Windows Explorer.

### `Clipboard`
- Klick auf den Button legt den angegebenen Text direkt in die Zwischenablage.

## 8) Kopfbereich zum Hinzufügen neuer Einträge
`portal.hta` soll einen oberen Eingabebereich erhalten, über den neue Einträge bequem in `portaldb.txt` geschrieben werden können:
- Eingabe für **Name**
- Auswahl/Eingabe für **Typ** (`LocalLink`, `Clipboard`, ...)
- Eingabe für **Wert**
- Schaltfläche „Hinzufügen/Speichern“

Beim Speichern wird eine neue Zeile im Codex-Format an `portaldb.txt` angehängt.

## 9) Dokumentationsanspruch
Die spätere Implementierung soll vollständig als aktuelle Zustandsdokumentation beschrieben werden:
- kurze Erklärung jeder Funktion/Komponente
- klare Beschreibung von Datenfluss und Dateiverhalten
- Fokus auf „Wie es jetzt funktioniert“, nicht auf Änderungshistorie
