# FHIR Validation Workflow

Dieses Projekt stellt einen automatisierten GitHub Actions Workflow zur Validierung von FHIR-Bundles bereit.

## Features

- **Automatische FHIR-Validierung**: Lädt den offiziellen FHIR Validator herunter und validiert FHIR-Ressourcen
- **Markdown-Reporting**: Nutzt den [validation-outcome-markdown-renderer](https://github.com/patrick-werner/validation-outcome-markdown-renderer) für lesbare Validierungsergebnisse
- **GitHub Actions Integration**: Automatische Ausführung bei Push/Pull Request
- **Artifacts**: Validierungsergebnisse werden als Artifacts gespeichert

## Projektstruktur

```
.
├── .github/
│   └── workflows/
│       └── validate-fhir.yml    # GitHub Actions Workflow
├── input/
│   └── bundle.json              # FHIR Bundle zur Validierung
├── output/                      # Validierungsergebnisse (wird generiert)
└── README.md
```

## Verwendung

### 1. Repository erstellen

Erstelle ein neues GitHub Repository und pushe diesen Code:

```bash
git init
git add .
git commit -m "Initial commit: FHIR validation workflow"
git branch -M main
git remote add origin https://github.com/DEIN-USERNAME/DEIN-REPO.git
git push -u origin main
```

### 2. FHIR Bundle hinzufügen

Platziere deine FHIR-Bundles im `input/`-Verzeichnis. Ein Beispiel-Bundle ist bereits vorhanden (`bundle.json`).

### 3. Workflow ausführen

Der Workflow wird automatisch ausgeführt bei:
- Push auf den `main`-Branch
- Pull Requests auf den `main`-Branch
- Manueller Auslösung über "Actions" → "FHIR Validation" → "Run workflow"

### 4. Ergebnisse ansehen

Nach der Ausführung:
1. Gehe zu "Actions" in deinem Repository
2. Klicke auf den Workflow-Run
3. Sieh dir das Validierungsreport im Job-Summary an
4. Lade die Artifacts für detaillierte JSON- und Markdown-Berichte herunter

## Workflow-Details

Der Workflow führt folgende Schritte aus:

1. **Checkout**: Repository-Code auschecken
2. **Java Setup**: Java 17 installieren
3. **Validator Download**: Neueste Version des FHIR Validators herunterladen
4. **Validierung**: FHIR Bundle gegen FHIR R4 (4.0.1) validieren
5. **Renderer Build**: validation-outcome-markdown-renderer kompilieren
6. **Markdown-Generierung**: Validierungsergebnis in Markdown konvertieren
7. **Report anzeigen**: Ergebnis im GitHub Actions Summary anzeigen
8. **Artifacts hochladen**: Alle Ergebnisse als Download bereitstellen

## Anpassungen

### FHIR-Version ändern

Editiere `.github/workflows/validate-fhir.yml` und ändere den `-version`-Parameter:

```yaml
java -jar validator_cli.jar input/bundle.json -version 4.3.0 -output output/validation-outcome.json
```

Unterstützte Versionen: `1.0.2`, `1.4.0`, `3.0.2`, `4.0.1`, `4.3.0`, `5.0.0`

### Mehrere Bundles validieren

Füge weitere Dateien im `input/`-Verzeichnis hinzu und erweitere den Workflow-Schritt:

```yaml
- name: Validate FHIR Bundles
  run: |
    for file in input/*.json; do
      java -jar validator_cli.jar "$file" -version 4.0.1 -output "output/$(basename "$file" .json)-outcome.json"
    done
```

### Profile hinzufügen

Um gegen spezifische Profile zu validieren:

```yaml
java -jar validator_cli.jar input/bundle.json \
  -version 4.0.1 \
  -ig package.tgz \
  -profile http://example.org/fhir/StructureDefinition/MyProfile
```

## Voraussetzungen

- GitHub Repository
- FHIR-konforme JSON-Dateien im `input/`-Verzeichnis

## Lizenz

Dieses Projekt ist frei verfügbar. Bitte beachte die Lizenzen der verwendeten Tools:
- [FHIR Validator](https://github.com/hapifhir/org.hl7.fhir.core)
- [validation-outcome-markdown-renderer](https://github.com/patrick-werner/validation-outcome-markdown-renderer)

## Weitere Ressourcen

- [FHIR Validator Documentation](https://confluence.hl7.org/display/FHIR/Using+the+FHIR+Validator)
- [FHIR R4 Specification](http://hl7.org/fhir/R4/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
