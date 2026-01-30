# Tests

Kurzdokumentation zu den Tests des Projekts.

## Ziel

- Sicherstellen, dass Kernfunktionen korrekt arbeiten.
- Fehler frühzeitig erkennen.
- Wiederholbare und automatisierte Qualitätssicherung ermöglichen.

## Testumfang

- Unit-Tests: Prüfen einzelne Funktionen/Klassen.
- Integrations-Tests: Prüfen Zusammenspiel mehrerer Komponenten.
- (Optional) End-to-End-Tests: Prüfen komplette Benutzerflüsse.

## Voraussetzungen

- Windows (PowerShell oder CMD)
- Test-Framework/Runner installiert (z. B. Jest/Mocha, xUnit/NUnit, PyTest – je nach Technologie)
- Abhängigkeiten installiert

## Projektstruktur (Beispiel/Empfehlung)

- `src/` – Quellcode
- `tests/` – Testfälle (Unit/Integration)
- `coverage/` – Testabdeckung (optional)

## Namenskonventionen

- Testdateien spiegeln die zu testende Datei/Komponente wider.
  - Beispiel: `src/calculator.js` → `tests/calculator.test.js`
- Klarer, beschreibender Testname (z. B. „should add two numbers“).

## Setup

1. Abhängigkeiten installieren.
   - Beispiel Node.js:
     ```powershell
     npm install
     ```
   - Beispiel .NET:
     ```powershell
     dotnet restore
     ```
   - Beispiel Python:
     ```powershell
     pip install -r requirements.txt
     ```

## Tests ausführen

- Führen Sie die zu Ihrem Stack passenden Befehle aus (nicht benötigte Beispiele entfernen):
  - Node.js (Jest/Mocha):

    ```powershell
    npm test
    ```

  - .NET:

    ```powershell
    dotnet test
    ```

  - Python (PyTest):
    ```powershell
    python -m pytest
    ```

## Coverage (optional)

- Testabdeckung erzeugen, falls unterstützt:
  - Node.js (Jest):
    ```powershell
    npm test -- --coverage
    ```
  - Python (pytest-cov):
    ```powershell
    python -m pytest --cov=src --cov-report=term-missing
    ```
