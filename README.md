# Eisenhower – Todo-Matrix auf Basis einer lokalen JSON-Datei

## Einrichten
1. **Hosten:** Ordnerinhalt in ein GitHub-Repo legen, unter *Settings → Pages* den Branch `main` / Root aktivieren.
   HTTPS ist Pflicht für den Dateizugriff, `file://` funktioniert nicht.
2. **Sync-Ordner:** Syncthing (Android: „Syncthing-Fork") installieren, einen Ordner z. B. `Todos` mit dem PC teilen,
   die mitgelieferte `todos.json` hineinlegen.
3. **Auf dem Handy:** Seite in Chrome öffnen → ⋮ → „Zum Startbildschirm hinzufügen" (installiert die App).
4. In der App ⋯ → **Datei öffnen** → `todos.json` im Syncthing-Ordner wählen → Schreibzugriff erlauben.
   Nach einem Neustart fragt Chrome evtl. erneut; dann reicht ein Tap auf das Banner „Erlauben".

## Bedienung
- **+** im Quadranten: neue Aufgabe. Tippen auf eine Karte: bearbeiten (Notizen, Tags, Quadrant).
- **Lange drücken und ziehen:** Karte in anderen Quadranten oder auf ✓ (oben rechts) verschieben.
- **Kreis antippen:** erledigt → Archiv (✓). Dort zurückholen oder löschen.
- Tag-Chips oben filtern die Matrix.

## Dateiformat (für n8n, Claude, Skripte)
```json
{ "schema": "eisenhower-todos", "version": 1, "updatedAt": "ISO-Zeit",
  "tasks": [{ "id": "uuid", "title": "…", "notes": "…", "tags": ["arbeit"],
              "urgent": true, "important": true, "done": false, "doneAt": null,
              "deleted": false, "order": 1759740000000,
              "createdAt": "ISO-Zeit", "updatedAt": "ISO-Zeit" }] }
```
Quadrant = `urgent` + `important` (wird nicht gespeichert, sondern abgeleitet).

**Regeln für externe Schreiber:**
- Jede geänderte Aufgabe bekommt ein neues `updatedAt` (ISO, UTC). Sonst gewinnt beim Zusammenführen der ältere Stand.
- Neue Aufgaben brauchen eine eindeutige `id`.
- Löschen = `"deleted": true` setzen (nicht entfernen), sonst taucht die Aufgabe vom Handy wieder auf.
  Gelöschte Einträge werden nach 30 Tagen automatisch entfernt.

## Wie gespeichert wird
Vor jedem Schreiben liest die App die Datei neu und führt pro Aufgabe zusammen (neueres `updatedAt` gewinnt).
Beim Zurückkehren in die App wird die Datei neu eingelesen. Ist die Datei kein gültiges JSON, schreibt die App
**nicht** darüber, sondern zeigt einen Fehler.

Grenze: Ändern Handy und PC *dieselbe* Aufgabe, bevor Syncthing synchronisiert hat, gewinnt die neuere Änderung;
Syncthing kann zusätzlich eine `.sync-conflict`-Kopie anlegen. Die lässt sich über ⋯ → „JSON einlesen" zusammenführen.

## Ohne Dateizugriff
Unterstützt ein Browser die File System Access API nicht, läuft die App im Browser-Speicher.
Dann über ⋯ → „Kopie herunterladen" / „JSON einlesen" sichern.
