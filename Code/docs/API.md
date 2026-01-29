# API Referenz

Dieses Dokument enthält detaillierte Informationen zu allen API-Endpunkten des Ticketsystems.

## Basis-URL

```
http://localhost:6001
```

## Interaktive Dokumentation

Für interaktive API-Tests können Sie die Swagger UI unter folgender Adresse aufrufen:

```
http://localhost:6001/api-docs
```

---

## Mitarbeiter-Endpunkte

Der Mitarbeiter-Service verwaltet Mitarbeiterdaten einschliesslich ihrer Details und Skill-Levels.

### Mitarbeiter erstellen

Erstellt einen neuen Mitarbeiter im System.

**Endpunkt:** `POST /api/employees`

**Request Body:**

```json
{
  "vorname": "Max",
  "nachname": "Mustermann",
  "beitrittsdatum": "2021-05-15",
  "skillLevel": 4
}
```

**Request Felder:**

| Feld             | Typ     | Erforderlich | Beschreibung                     |
| ---------------- | ------- | ------------ | -------------------------------- |
| `vorname`        | String  | Ja           | Vorname des Mitarbeiters         |
| `nachname`       | String  | Ja           | Nachname des Mitarbeiters        |
| `beitrittsdatum` | Datum   | Ja           | Beitrittsdatum (ISO 8601 Format) |
| `skillLevel`     | Integer | Ja           | Skill-Level (1-5)                |

**Validierungsregeln:**

- `vorname`: Erforderlich, muss ein String sein
- `nachname`: Erforderlich, muss ein String sein
- `beitrittsdatum`: Erforderlich, muss ein gültiges Datum sein
- `skillLevel`: Erforderlich, muss eine Ganzzahl zwischen 1 und 5 sein

**Erfolgreiche Antwort:**

- **Statuscode:** `201 Created`
- **Response Body:**

```json
{
  "_id": "507f1f77bcf86cd799439011",
  "vorname": "Max",
  "nachname": "Mustermann",
  "beitrittsdatum": "2021-05-15T00:00:00.000Z",
  "skillLevel": 4,
  "createdAt": "2024-01-20T10:30:00.000Z",
  "updatedAt": "2024-01-20T10:30:00.000Z"
}
```

**Fehlerantworten:**

- **Statuscode:** `400 Bad Request`
  - Fehlende erforderliche Felder
  - Ungültiges Skill-Level (nicht zwischen 1-5)
  - Ungültiges Datumsformat

### Alle Mitarbeiter abrufen

Ruft eine Liste aller Mitarbeiter ab.

**Endpunkt:** `GET /api/employees`

**Erfolgreiche Antwort:**

- **Statuscode:** `200 OK`
- **Response Body:**

```json
[
  {
    "_id": "507f1f77bcf86cd799439011",
    "vorname": "Max",
    "nachname": "Mustermann",
    "beitrittsdatum": "2021-05-15T00:00:00.000Z",
    "skillLevel": 4,
    "createdAt": "2024-01-20T10:30:00.000Z",
    "updatedAt": "2024-01-20T10:30:00.000Z"
  },
  {
    "_id": "507f1f77bcf86cd799439012",
    "vorname": "Anna",
    "nachname": "Schmidt",
    "beitrittsdatum": "2022-03-10T00:00:00.000Z",
    "skillLevel": 5,
    "createdAt": "2024-01-20T10:31:00.000Z",
    "updatedAt": "2024-01-20T10:31:00.000Z"
  }
]
```

### Mitarbeiter nach ID abrufen

Ruft einen bestimmten Mitarbeiter anhand seiner ID ab.

**Endpunkt:** `GET /api/employees/:id`

**URL Parameter:**

| Parameter | Typ    | Beschreibung                      |
| --------- | ------ | --------------------------------- |
| `id`      | String | MongoDB ObjectId des Mitarbeiters |

**Erfolgreiche Antwort:**

- **Statuscode:** `200 OK`
- **Response Body:**

```json
{
  "_id": "507f1f77bcf86cd799439011",
  "vorname": "Max",
  "nachname": "Mustermann",
  "beitrittsdatum": "2021-05-15T00:00:00.000Z",
  "skillLevel": 4,
  "createdAt": "2024-01-20T10:30:00.000Z",
  "updatedAt": "2024-01-20T10:30:00.000Z"
}
```

**Fehlerantworten:**

- **Statuscode:** `400 Bad Request` - Ungültiges ID-Format
- **Statuscode:** `404 Not Found` - Mitarbeiter existiert nicht

---

## Ticket-Endpunkte

Der Ticket-Service verwaltet Support-Tickets mit Status-Tracking und Mitarbeiter-Zuweisungen.

### Ticket erstellen

Erstellt ein neues Ticket, das einem Mitarbeiter zugewiesen wird.

**Endpunkt:** `POST /api/tickets`

**Request Body:**

```json
{
  "status": "Open",
  "titel": "Bugfix erforderlich",
  "text": "Login-Problem auf mobilen Geräten beheben",
  "mitarbeiterId": "507f1f77bcf86cd799439011"
}
```

**Request Felder:**

| Feld            | Typ    | Erforderlich | Beschreibung                                    |
| --------------- | ------ | ------------ | ----------------------------------------------- |
| `status`        | String | Ja           | Ticket-Status (Open, In Progress, Review, Done) |
| `titel`         | String | Ja           | Ticket-Titel                                    |
| `text`          | String | Ja           | Ticket-Beschreibung                             |
| `mitarbeiterId` | String | Ja           | ID des zugewiesenen Mitarbeiters                |
| `reviewDatum`   | Datum  | Nein         | Review-Datum (nur bei Status Review oder Done)  |
| `doneDatum`     | Datum  | Nein         | Abschlussdatum (nur bei Status Done)            |

**Validierungsregeln:**

- `status`: Erforderlich, muss einer von: "Open", "In Progress", "Review", "Done" sein
- `titel`: Erforderlich, muss ein String sein
- `text`: Erforderlich, muss ein String sein
- `mitarbeiterId`: Erforderlich, muss auf einen existierenden Mitarbeiter verweisen
- `reviewDatum`: Optional, nur erlaubt wenn Status "Review" oder "Done" ist
- `doneDatum`: Optional, nur erlaubt wenn Status "Done" ist

**Erfolgreiche Antwort:**

- **Statuscode:** `201 Created`
- **Response Body:**

```json
{
  "_id": "507f1f77bcf86cd799439020",
  "status": "Open",
  "titel": "Bugfix erforderlich",
  "text": "Login-Problem auf mobilen Geräten beheben",
  "mitarbeiterId": "507f1f77bcf86cd799439011",
  "createdAt": "2024-01-20T10:30:00.000Z",
  "updatedAt": "2024-01-20T10:30:00.000Z"
}
```

**Fehlerantworten:**

- **Statuscode:** `400 Bad Request`
  - Fehlende erforderliche Felder
  - Ungültiger Status-Wert
  - Ungültiges Datum für Status (z.B. reviewDatum bei Status "Open")
  - Mitarbeiter existiert nicht
- **Statuscode:** `404 Not Found` - Referenzierter Mitarbeiter nicht gefunden

### Alle Tickets abrufen

Ruft eine Liste aller Tickets ab.

**Endpunkt:** `GET /api/tickets`

**Erfolgreiche Antwort:**

- **Statuscode:** `200 OK`
- **Response Body:**

```json
[
  {
    "_id": "507f1f77bcf86cd799439020",
    "status": "Open",
    "titel": "Bugfix erforderlich",
    "text": "Login-Problem auf mobilen Geräten beheben",
    "mitarbeiterId": "507f1f77bcf86cd799439011",
    "createdAt": "2024-01-20T10:30:00.000Z",
    "updatedAt": "2024-01-20T10:30:00.000Z"
  },
  {
    "_id": "507f1f77bcf86cd799439021",
    "status": "Done",
    "titel": "Dokumentation aktualisieren",
    "text": "API-Dokumentation aktualisieren",
    "mitarbeiterId": "507f1f77bcf86cd799439012",
    "reviewDatum": "2024-01-19T14:00:00.000Z",
    "doneDatum": "2024-01-20T09:00:00.000Z",
    "createdAt": "2024-01-18T10:30:00.000Z",
    "updatedAt": "2024-01-20T09:00:00.000Z"
  }
]
```

### Ticket nach ID abrufen

Ruft ein bestimmtes Ticket anhand seiner ID ab.

**Endpunkt:** `GET /api/tickets/:id`

**URL Parameter:**

| Parameter | Typ    | Beschreibung                 |
| --------- | ------ | ---------------------------- |
| `id`      | String | MongoDB ObjectId des Tickets |

**Erfolgreiche Antwort:**

- **Statuscode:** `200 OK`
- **Response Body:**

```json
{
  "_id": "507f1f77bcf86cd799439020",
  "status": "Open",
  "titel": "Bugfix erforderlich",
  "text": "Login-Problem auf mobilen Geräten beheben",
  "mitarbeiterId": "507f1f77bcf86cd799439011",
  "createdAt": "2024-01-20T10:30:00.000Z",
  "updatedAt": "2024-01-20T10:30:00.000Z"
}
```

**Fehlerantworten:**

- **Statuscode:** `400 Bad Request` - Ungültiges ID-Format
- **Statuscode:** `404 Not Found` - Ticket existiert nicht

### Ticket aktualisieren

Aktualisiert ein bestehendes Ticket. Wird häufig verwendet, um den Status zu ändern und Review-/Abschlussdaten hinzuzufügen.

**Endpunkt:** `PUT /api/tickets/:id`

**URL Parameter:**

| Parameter | Typ    | Beschreibung                 |
| --------- | ------ | ---------------------------- |
| `id`      | String | MongoDB ObjectId des Tickets |

**Request Body:**

```json
{
  "status": "Done",
  "doneDatum": "2024-01-20T15:30:00Z"
}
```

**Erfolgreiche Antwort:**

- **Statuscode:** `200 OK`
- **Response Body:**

```json
{
  "_id": "507f1f77bcf86cd799439020",
  "status": "Done",
  "titel": "Bugfix erforderlich",
  "text": "Login-Problem auf mobilen Geräten beheben",
  "mitarbeiterId": "507f1f77bcf86cd799439011",
  "reviewDatum": "2024-01-19T14:00:00.000Z",
  "doneDatum": "2024-01-20T15:30:00.000Z",
  "createdAt": "2024-01-20T10:30:00.000Z",
  "updatedAt": "2024-01-20T15:30:00.000Z"
}
```

**Fehlerantworten:**

- **Statuscode:** `400 Bad Request`
  - Ungültiges ID-Format
  - Ungültiger Status-Übergang
  - Ungültiges Datum für Status
- **Statuscode:** `404 Not Found` - Ticket existiert nicht

### Ticket löschen

Löscht ein Ticket aus dem System.

**Endpunkt:** `DELETE /api/tickets/:id`

**URL Parameter:**

| Parameter | Typ    | Beschreibung                 |
| --------- | ------ | ---------------------------- |
| `id`      | String | MongoDB ObjectId des Tickets |

**Erfolgreiche Antwort:**

- **Statuscode:** `200 OK`
- **Response Body:**

```json
{
  "message": "Ticket deleted successfully"
}
```

**Fehlerantworten:**

- **Statuscode:** `400 Bad Request` - Ungültiges ID-Format
- **Statuscode:** `404 Not Found` - Ticket existiert nicht

---

## Status-Übergänge

Tickets folgen einem spezifischen Workflow mit Status-Übergängen:

```
Open → In Progress → Review → Done
```

### Status-Regeln

- **Open**: Anfangszustand, keine Datumsbeschränkungen
- **In Progress**: Arbeit hat begonnen, keine Datumsbeschränkungen
- **Review**: In Überprüfung, kann `reviewDatum` haben
- **Done**: Abgeschlossen, muss `doneDatum` haben, kann `reviewDatum` haben

### Datums-Validierung

- `reviewDatum` ist nur erlaubt, wenn der Status "Review" oder "Done" ist
- `doneDatum` ist nur erlaubt, wenn der Status "Done" ist
- Daten müssen im ISO 8601 Format sein

---

## Fehlerantwort-Format

Alle Fehlerantworten folgen diesem Format:

```json
{
  "error": "Fehlermeldung auf Deutsch"
}
```

Häufige Fehlermeldungen:

- "Mitarbeiter nicht gefunden": Mitarbeiter wurde nicht gefunden
- "Ungültiges SkillLevel. Muss zwischen 1 und 5 liegen.": Ungültiges Skill-Level
- "reviewDatum ist nur erlaubt, wenn Status 'Review' oder 'Done' ist": Review-Datum nur bei Review oder Done Status erlaubt

---

## Datentypen

### Datumsformat

Alle Daten verwenden das ISO 8601 Format:

- `YYYY-MM-DD` für nur Datum
- `YYYY-MM-DDTHH:mm:ss.sssZ` für Zeitstempel

Beispiele:

- `2021-05-15`
- `2024-01-20T10:30:00.000Z`

### ObjectId Format

MongoDB ObjectIds sind 24-stellige hexadezimale Zeichenketten:

- Beispiel: `507f1f77bcf86cd799439011`
