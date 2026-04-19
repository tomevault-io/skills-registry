---
name: com-port-management
description: Ensure the serial COM port is available before flashing or monitoring an ESP32 device Use when this capability is needed.
metadata:
  author: sparkeh9
---

# COM Port Management

## When to Use

- Before running `idf.py flash` or `idf.py monitor`
- When flashing fails with `PermissionError(13, 'Access is denied.')` or `could not open port 'COMx'`
- After a previous monitor session was interrupted or crashed

## Step 1: Identify the COM Port

```powershell
// turbo
[System.IO.Ports.SerialPort]::GetPortNames()
```

The board typically appears as `COM5` (check Device Manager if unsure).

## Step 2: Check for Processes Holding the Port

Stale Python processes from previous `idf.py monitor` sessions are the most common cause:

```powershell
// turbo
Get-Process -Name python -ErrorAction SilentlyContinue | Select-Object Id, ProcessName, StartTime | Format-Table -AutoSize
```

## Step 3: Kill Stale Processes

Kill Python processes older than a few minutes (these are leftover monitor/flash sessions):

```powershell
Get-Process -Name python -ErrorAction SilentlyContinue | Where-Object { $_.StartTime -lt (Get-Date).AddMinutes(-5) } | Stop-Process -Force -PassThru | Select-Object Id, ProcessName
```

> **NOTE:** Only kill processes older than 5 minutes to avoid killing active builds. Adjust the threshold as needed.

## Step 4: Verify Port is Free

Try opening the port briefly:

```powershell
// turbo
try {
    $port = New-Object System.IO.Ports.SerialPort "COM5", 115200
    $port.Open()
    $port.Close()
    Write-Host "COM5 is available"
} catch {
    Write-Host "COM5 is still locked: $($_.Exception.Message)"
}
```

## Common Causes

| Symptom | Cause | Fix |
|---|---|---|
| `PermissionError(13, 'Access is denied.')` | Previous `idf.py monitor` still running | Kill stale Python processes |
| `could not open port` | Port doesn't exist or device unplugged | Check USB cable, run `GetPortNames()` |
| Port locked after crash | esptool/monitor didn't clean up | Kill all Python processes, replug USB |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sparkeh9) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
