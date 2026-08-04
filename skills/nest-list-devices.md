---
name: List and read Nest devices
description: Enumerate the Nest devices a user authorized and read their current trait state via the Smart Device Management API.
api: Smart Device Management (SDM) API
operations:
  - enterprises.devices.list
  - enterprises.devices.get
  - enterprises.structures.list
---

# List and read Nest devices

Use the SDM API to discover the devices a user has authorized for your Device
Access project and inspect their live state.

## Prerequisites
- A Device Access project (project id) from the Device Access Console.
- A Google OAuth 2.0 access token with scope
  `https://www.googleapis.com/auth/sdm.service`, obtained through the Partner
  Connections Manager consent flow (see `authentication/nest-authentication.yml`).
- Base URL: `https://smartdevicemanagement.googleapis.com`.

## Steps
1. **List devices** — `GET /v1/enterprises/{project-id}/devices`
   (`enterprises.devices.list`). Page with `pageSize` / `pageToken`; read
   `nextPageToken` to continue.
2. **Inspect a device** — `GET /v1/enterprises/{project-id}/devices/{device-id}`
   (`enterprises.devices.get`). Read the `traits` map
   (`sdm.devices.traits.*`) for temperature, humidity, connectivity, camera,
   or doorbell state.
3. **(Optional) Group by home** — `GET /v1/enterprises/{project-id}/structures`
   (`enterprises.structures.list`) and the rooms under each to map devices to
   physical locations.

## Rules
- Send the token as `Authorization: Bearer <token>`.
- Handle Google error model responses (`error.status` = `UNAUTHENTICATED`,
  `PERMISSION_DENIED`, `NOT_FOUND`, `RESOURCE_EXHAUSTED`) — see
  `errors/nest-problem-types.yml`.
- Back off on `429 RESOURCE_EXHAUSTED` (per-project quota).
