---
name: Look up a threat indicator in OTX
description: >-
  Given an indicator of compromise (IP, domain, hostname, URL, file hash, or CVE),
  retrieve its threat detail from AlienVault OTX and find related community pulses.
api: openapi/alienvault-otx-openapi.yml
operations:
  - getIndicatorDetails
  - searchPulses
  - getPulseDetails
---

# Look up a threat indicator in OTX

Use the AlienVault OTX DirectConnect API to enrich an indicator of compromise.

## Auth
Send every request with the header `X-OTX-API-KEY: <your key>` (from your OTX account
settings). Base URL is `https://otx.alienvault.com/api/v1`.

## Steps
1. **Get indicator detail** — call `getIndicatorDetails`
   (`GET /api/v1/indicators/{type}/{indicator}/{section}`). Set `type` to one of
   `IPv4`, `IPv6`, `domain`, `hostname`, `url`, `file`, `cve`; `indicator` to the value;
   `section` to `general` first (then `malware`, `passive_dns`, `reputation`, `url_list`,
   `geo` as needed).
2. **Find related pulses** — call `searchPulses`
   (`GET /api/v1/search/pulses?q={indicator}`) to see which community pulses reference it.
3. **Inspect a pulse** — for an interesting result, call `getPulseDetails`
   (`GET /api/v1/pulses/{pulse_id}`) to read its description, TLP, tags, and author.

## Conventions
- List responses are paginated with `limit`/`page`; follow the `next` URL to page.
- Errors are standard HTTP: `403` = missing/invalid API key, `404` = unknown indicator.
  See `errors/alienvault-problem-types.yml`.
