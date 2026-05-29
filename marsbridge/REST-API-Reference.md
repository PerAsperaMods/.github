# MarsBridge REST API Reference

The MarsBridge plugin exposes an HTTP server on **port 8080** inside Per Aspera. All responses are JSON. All timestamps are ISO 8601.

---

## Base URL

```
http://localhost:8080
```

---

## Endpoints

### GET /mars/health

Checks that the HTTP server is running.

**Response**
```json
{
  "status": "healthy",
  "version": "1.0.0",
  "timestamp": "2026-01-01T12:00:00Z"
}
```

---

### GET /mars/status

Returns the current state of the planet.

**Response**
```json
{
  "success": true,
  "data": {
    "temperature": 15.5,
    "atmosphericPressure": 0.8,
    "resources": {
      "water": 1000.0,
      "oxygen": 500.0
    }
  },
  "timestamp": "2026-01-01T12:00:00Z"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `temperature` | `float` | Average surface temperature in °C |
| `atmosphericPressure` | `float` | Total atmospheric pressure in atm |
| `resources` | `object` | Key resource stock levels |

---

### GET /mars/atmosphere/pressure

Returns total atmospheric pressure with breathability status.

**Response**
```json
{
  "success": true,
  "message": "Atmospheric pressure retrieved successfully",
  "data": {
    "totalPressure": 0.5,
    "unit": "atm",
    "isBreathable": false,
    "timestamp": "2026-01-01T12:00:00Z"
  }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `totalPressure` | `float` | Sum of all gas partial pressures |
| `unit` | `string` | Always `"atm"` |
| `isBreathable` | `bool` | `true` when O2 > 0.2 atm and total > 0.9 atm |

---

### GET /mars/atmosphere/composition

Returns the full atmospheric gas breakdown.

**Response**
```json
{
  "success": true,
  "message": "Atmospheric composition retrieved successfully",
  "data": {
    "gases": {
      "CO2": {
        "partialPressure": 0.4,
        "percentage": 80.0,
        "unit": "atm"
      },
      "O2": {
        "partialPressure": 0.05,
        "percentage": 10.0,
        "unit": "atm"
      },
      "N2": {
        "partialPressure": 0.08,
        "percentage": 16.0,
        "unit": "atm"
      }
    },
    "totalPressure": 0.5,
    "temperature": 15.5,
    "unit": "atm",
    "timestamp": "2026-01-01T12:00:00Z"
  }
}
```

---

### POST /mars/climate

Sends a climate modification command to Per Aspera.

> **Note**: SDK integration is in progress. Commands are accepted and logged but not yet applied to the live simulation.

**Request body**
```json
{
  "action": "optimize",
  "targetTemperature": 20.0,
  "parameters": {
    "duration": 3600
  }
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `action` | `string` | Yes | Command name (e.g. `"optimize"`, `"heat"`, `"cool"`) |
| `targetTemperature` | `float` | No | Target temperature in °C |
| `parameters` | `object` | No | Additional command-specific options |

**Response**
```json
{
  "success": true,
  "message": "Climate command received",
  "timestamp": "2026-01-01T12:00:00Z"
}
```

---

### POST /mars/resources

Triggers a resource import from Satisfactory into Per Aspera.

> **Note**: SDK integration is in progress. Resources are queued but not yet applied via the Commands SDK.

**Request body**
```json
{
  "action": "export",
  "resourceType": "water",
  "amount": 100.0,
  "destination": "mars"
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `action` | `string` | Yes | Always `"export"` for Satisfactory → Mars transfers |
| `resourceType` | `string` | Yes | Per Aspera resource key (e.g. `"water"`, `"iron"`) |
| `amount` | `float` | Yes | Quantity to transfer |
| `destination` | `string` | No | Always `"mars"` |

**Response**
```json
{
  "success": true,
  "message": "Resource command received",
  "timestamp": "2026-01-01T12:00:00Z"
}
```

---

## Data Models

These C# classes define all request and response shapes:

```csharp
public class ApiResponse<T>
{
    public bool Success { get; set; }
    public string Message { get; set; }
    public T Data { get; set; }
    public DateTime Timestamp { get; set; }
}

public class ClimateCommand
{
    public string Action { get; set; }
    public float TargetTemperature { get; set; }
    public Dictionary<string, object> Parameters { get; set; }
}

public class PlanetStatus
{
    public float Temperature { get; set; }
    public float AtmosphericPressure { get; set; }
    public Dictionary<string, float> Resources { get; set; }
}

public class ResourceCommand
{
    public string Action { get; set; }
    public string ResourceType { get; set; }
    public float Amount { get; set; }
    public string Destination { get; set; }
}
```

---

## Error Responses

All errors return a standard envelope:

```json
{
  "success": false,
  "message": "Description of what went wrong",
  "timestamp": "2026-01-01T12:00:00Z"
}
```

Common HTTP status codes:

| Code | Meaning |
|------|---------|
| 200 | Success |
| 400 | Invalid request body |
| 500 | Internal server error — check `BepInEx/LogOutput.log` |
