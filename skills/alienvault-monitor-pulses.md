---
name: Monitor subscribed threat pulses
description: >-
  Poll AlienVault OTX for new and updated threat pulses the user is subscribed to, and
  pull their indicators for ingestion into defensive tooling.
api: openapi/alienvault-otx-openapi.yml
operations:
  - getSubscribedPulses
  - getPulseEvents
  - getPulseIndicators
---

# Monitor subscribed threat pulses

Continuously ingest fresh threat intelligence from the pulses a user follows in OTX.

## Auth
Send `X-OTX-API-KEY: <your key>` on every request. Base URL is
`https://otx.alienvault.com/api/v1`.

## Steps
1. **Initial sync** — call `getSubscribedPulses`
   (`GET /api/v1/pulses/subscribed`) and page through all results (`limit`/`page`,
   follow `next`).
2. **Incremental updates** — on later runs pass `modified_since` (or use
   `getPulseEvents`, `GET /api/v1/pulses/events`, with `since`) to fetch only pulses
   changed after your last checkpoint. Store the newest `modified` timestamp as the
   next checkpoint.
3. **Pull indicators** — for each new/updated pulse call `getPulseIndicators`
   (`GET /api/v1/pulses/{pulse_id}/indicators`) and hand the IOCs to your firewall/SIEM.

## Conventions
- Change-feed polling via `modified_since`/`since` is the OTX-recommended incremental
  pattern; see `conventions/alienvault-conventions.yml`.
- There is no webhook/push surface — poll on an interval.
- `403` means the API key is missing or invalid.
