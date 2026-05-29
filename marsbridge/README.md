# MarsBridge

MarsBridge is a cross-game integration project that connects **Satisfactory** and **Per Aspera** via a REST HTTP API. Resources produced in Satisfactory can be sent to Mars in Per Aspera, and the Martian climate state can be queried in real time.

---

## Architecture

```
Satisfactory Mod
  └── HTTP POST/GET
        └── MarsBridge REST API (port 8080)
              └── Per Aspera SDK (Wrappers, Climate, Commands)
                    └── Mars game state
```

### Components

| Component | Location | Role |
|-----------|----------|------|
| `MarsBridgeRestAPIPlugin.cs` | `Individual-Mods/MarsBridge.RestAPI/` | BepInEx plugin — starts the HTTP server |
| `ApiModels.cs` | `Models/` | JSON request/response data classes |
| `Test-RestAPI.ps1` | `Individual-Mods/MarsBridge.RestAPI/` | PowerShell test script for all endpoints |

---

## What Works Today

- HTTP server starts automatically on port 8080 when Per Aspera loads
- `GET /mars/health` — confirms the server is running
- `GET /mars/status` — returns current planet temperature and pressure
- `GET /mars/atmosphere/pressure` — total atmospheric pressure
- `GET /mars/atmosphere/composition` — full gas breakdown (CO2, O2, N2…)
- `POST /mars/climate` — sends a climate modification command
- `POST /mars/resources` — triggers a resource import

See [REST API Reference](REST-API-Reference.md) for full endpoint documentation.

---

## What Remains To Do

### High Priority — SDK Integration
- Replace placeholder values in `/mars/climate` with real calls to `PerAspera.GameAPI.Climate`
- Wire `/mars/resources` to the Commands SDK (`ImportResource` command)
- Subscribe to Per Aspera events for real-time state pushes

### Medium Priority — Satisfactory Side
- Build a Satisfactory mod that calls the MarsBridge API automatically
- Track production totals and POST exports when thresholds are met

### Low Priority — Hardening
- Add API token authentication
- Expose a `/mars/events` Server-Sent Events stream
- Add structured error codes to all responses

---

## Documentation

- [Getting Started](Getting-Started.md) — build, deploy, and test
- [REST API Reference](REST-API-Reference.md) — all endpoints with request/response schemas
