---
name: Control a Nest thermostat
description: Read a Nest thermostat's state and change its mode or temperature setpoint via the SDM executeCommand method.
api: Smart Device Management (SDM) API
operations:
  - enterprises.devices.get
  - enterprises.devices.executeCommand
---

# Control a Nest thermostat

Adjust a Nest thermostat's mode and setpoint using trait commands.

## Prerequisites
- Google OAuth 2.0 access token with scope
  `https://www.googleapis.com/auth/sdm.service`.
- The device resource name `enterprises/{project-id}/devices/{device-id}` of a
  Nest Thermostat (from the list-devices skill).

## Steps
1. **Read current state** — `GET /v1/enterprises/{project-id}/devices/{device-id}`
   (`enterprises.devices.get`). Read
   `sdm.devices.traits.ThermostatMode` and
   `sdm.devices.traits.ThermostatTemperatureSetpoint`.
2. **Set the mode** — `POST /v1/enterprises/{project-id}/devices/{device-id}:executeCommand`
   (`enterprises.devices.executeCommand`) with command
   `sdm.devices.commands.ThermostatMode.SetMode` and params `{ "mode": "HEAT" }`
   (one of `HEAT`, `COOL`, `HEATCOOL`, `OFF`).
3. **Set the temperature** — call `executeCommand` again with
   `sdm.devices.commands.ThermostatTemperatureSetpoint.SetHeat` (or `SetCool` /
   `SetRange`) and the target temperature in Celsius.

## Rules
- The thermostat must be in a compatible mode before a setpoint command is
  accepted (set mode first, then setpoint).
- `executeCommand` is **not** idempotent — do not blindly retry a successful
  command; re-read state on ambiguous failures (`errors/nest-problem-types.yml`).
- Expect `PERMISSION_DENIED` if the user did not grant thermostat control at
  consent time.
