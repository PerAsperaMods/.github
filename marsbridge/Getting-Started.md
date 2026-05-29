# Getting Started with MarsBridge

This guide covers building the MarsBridge plugin, deploying it to Per Aspera, and verifying that the REST API is up.

---

## Prerequisites

- Per Aspera installed with BepInEx 6
- .NET 6.0 SDK (`dotnet --version` should return `6.x`)
- Port **8080** available on your machine (not used by another process)

---

## Build

```powershell
cd F:\ModPeraspera\Individual-Mods\MarsBridge.RestAPI
dotnet build -c Release
```

The output DLL is placed in `bin\Release\net6.0\MarsBridge.RestAPI.dll`.

---

## Deploy

Copy the DLL to BepInEx:

```powershell
Copy-Item "bin\Release\net6.0\MarsBridge.RestAPI.dll" `
          "F:\SteamLibrary\steamapps\common\Per Aspera\BepInEx\plugins\"
```

Or use the VS Code task **SDK: Deploy DLLs to BepInX** if you added the project to the build pipeline.

---

## Verify the Plugin Loads

1. Launch Per Aspera
2. Load or start a game
3. Open `BepInEx\LogOutput.log` and search for:

```
[Info  : MarsBridge] HTTP server started on port 8080
```

If you see that line, the API is ready.

---

## Test All Endpoints

Run the included PowerShell test script:

```powershell
cd F:\ModPeraspera\Individual-Mods\MarsBridge.RestAPI
.\Test-RestAPI.ps1
```

The script calls every endpoint and prints pass/fail with response details.

---

## Manual Quick Tests

**Health check:**
```powershell
Invoke-RestMethod http://localhost:8080/mars/health
```

**Current planet status:**
```powershell
Invoke-RestMethod http://localhost:8080/mars/status
```

**Atmospheric composition:**
```powershell
Invoke-RestMethod http://localhost:8080/mars/atmosphere/composition
```

**Send a climate command:**
```powershell
$body = @{ action = "optimize"; targetTemperature = 20.0 } | ConvertTo-Json
Invoke-RestMethod -Method POST -Uri http://localhost:8080/mars/climate `
                  -Body $body -ContentType "application/json"
```

**Export resources from Satisfactory:**
```powershell
$body = @{ action = "export"; resourceType = "water"; amount = 100 } | ConvertTo-Json
Invoke-RestMethod -Method POST -Uri http://localhost:8080/mars/resources `
                  -Body $body -ContentType "application/json"
```

---

## Calling the API from a Satisfactory Mod (C#)

```csharp
using System.Net.Http;
using System.Text;
using System.Text.Json;

var client = new HttpClient();

// Export water to Mars
var command = new { action = "export", resourceType = "water", amount = 500f };
var json = JsonSerializer.Serialize(command);
var content = new StringContent(json, Encoding.UTF8, "application/json");
var response = await client.PostAsync("http://localhost:8080/mars/resources", content);
```

---

## Troubleshooting

| Symptom | Check |
|---------|-------|
| No log line about port 8080 | DLL not in `plugins/`, or BepInEx failed to load it — check top of `LogOutput.log` |
| `Connection refused` on port 8080 | Plugin loaded but server crashed — search `[Error]` near MarsBridge in the log |
| Port 8080 already in use | Change the port in `MarsBridgeRestAPIPlugin.cs` (look for `HttpListener` prefix) and rebuild |
| Response has `success: false` | Read `message` field; detailed error is also in `LogOutput.log` |
