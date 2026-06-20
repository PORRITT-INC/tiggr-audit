# TIGGR Cowork Sidecar — Live Capture Validation
**Porritt Inc. | 2026-06-20 | Author: Timothy Porritt**
**Status: VALIDATED — real desktop-client AI exchange captured and verifiable**

---

## Summary

On 2026-06-20 the TIGGR Cowork Sidecar captured a **live, full-content
request/response exchange** between the Cowork desktop client and the Anthropic
API, into its tamper-evident, hash-chained local audit database. This closes the
last open question from the white paper: the system captures not just background
polling but the **actual conversation payloads** of a first-party desktop AI client.

## What was validated

| Item | Value |
|---|---|
| Captured record id | **#60435** |
| Timestamp (UTC) | 2026-06-20T05:22:37Z |
| Direction / method | request (POST) |
| Endpoint | `https://api.anthropic.com/v1/messages?beta=true` |
| Payload size | **3,688,162 bytes** (full conversation context sent per turn) |
| Content | contains the operator's verbatim prompt text (confirmed via search) |
| Storage | `%LOCALAPPDATA%\PorrittInc\TIGGR\audit.db` (local, customer-controlled) |
| Integrity | SHA-256 payload digest + chain hash linked to prior record |

Confirmed by content search:

```
> python tiggr_find.py "how are you doing"
1 match(es):
#60435  2026-06-20T05:22:37Z  request  3688162B  https://api.anthropic.com/v1/messages?beta=true
   ..."content":"...where do i see this phrase in the audit trail."},{"role":"sys...
```

Full record is readable/verifiable locally with `python tiggr_verify.py --show 60435`.

## Root cause that had blocked content capture (and the fix)

Earlier, only `GET /v1/models` polls were captured — never the chat. Diagnosis:

- The Cowork desktop app is **Chromium/Electron-based**. Chromium **ignores the
  `HTTP_PROXY` / `HTTPS_PROXY` environment variables**, and the per-app
  `--proxy-server` flag did not reach the process that issues the API calls.
- Cowork **does** honor the **Windows system proxy** (WinINET). Setting the system
  proxy to `127.0.0.1:8791` routed its `/v1/messages` traffic through TIGGR.
- Because the local mitmproxy CA is in Trusted Root, the TLS was decryptable, so the
  full request body was recorded. (The app did **not** pin its certificate — a
  candidate hypothesis that the test ruled out.)

Reproduction: `Capture_Cowork_SystemProxy.ps1` (enable system proxy + relaunch),
then send a message, then `tiggr_find.py` / `tiggr_verify.py`. Revert with
`Disable_System_Proxy.ps1`.

## Significance

TIGGR now demonstrably provides a **customer-owned, tamper-evident audit of real
AI API exchanges from a production desktop client** — capturing the exact bytes
sent and received, hash-chained so any later alteration is detectable, entirely
local with no cloud or third party. This is the concrete evidence behind the
"audit-grade AI development workflow" claim.

## Honest scope & privacy

- This audits the operator's **own** usage of the AI API (the operator's side of
  the wire). It is not, and does not claim to be, visibility into the provider's
  internal systems.
- Captured bodies contain real prompts and may be sensitive; the database is kept
  **local**. For external submission, share the **metadata + verification output**
  (record id, size, endpoint, hashes, `tiggr_verify` result) rather than raw bodies.
- The system-proxy method routes all machine traffic through the local proxy while
  enabled; disable it when not actively auditing.

## Artifacts (reproducible)

`tiggr_sidecar.py` (capture) · `tiggr_verify.py` (chain verify / record view) ·
`tiggr_find.py` (content search) · `tiggr_tail.py` (live view) ·
`Capture_Cowork_SystemProxy.ps1` / `Disable_System_Proxy.ps1` (routing) ·
`TIGGR-Supervisor.ps1` (resilience + auto-start) · `audit.db` (hash-chained store).

---
*Porritt Inc., 2026 — Audit-grade AI development. Patent pending.*
