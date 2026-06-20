# TIGGR — Tamper-Evident Audit & Governance for AI Usage

**Porritt Inc. · Layton, UT · Patent pending**

> Prove what your AI sent and received — and prove the log itself was never altered.

TIGGR records every exchange between your AI clients and model APIs into an
append-only, cryptographically **hash-chained** ledger you control. Any later change to a
record breaks the chain at that exact entry, so the audit is **tamper-evident** and
**independently verifiable** — the evidence regulators, auditors, and customers increasingly ask for.

---

## 🔴 Live demo
**https://tiggr-dashboard.jollypond-4d6593f5.eastus.azurecontainerapps.io**

A hosted instance of the TIGGR verify dashboard (running on Azure Container Apps) showing a
verified hash chain over a sample dataset. The banner reads **CHAIN VERIFIED** when the
ledger's integrity checks pass end to end.

---

## Why it matters
Enterprises now run sensitive work through AI APIs with little independent, verifiable record
of it. The **EU AI Act (Article 12)** requires tamper-evident logging for high-risk AI, with
retention and queryability — organizations must be able to show *what* an AI was asked, *what*
it returned, and *that the record is intact*. TIGGR produces exactly that, **locally and
customer-controlled** — no cloud middleman holding your prompts.

## How it works (two synchronized views of one captured exchange)
1. **Human-readable audit trail** — every request/response in a familiar flow view
   (endpoint, status, size, decoded body for the data owner).
2. **Tamper-evident cryptographic record** — the same events as an opaque, hash-chained
   ledger: a SHA-256 digest per payload plus a chain value binding each record to the prior
   one. Alter any past record and the chain breaks at that point.

Capture options scale from a local endpoint agent to a managed AI-egress gateway to an
in-tenant enterprise deployment. Audit data stays in the customer's control.

## Validated at production scale
- **60,000+** real AI API exchanges recorded in continuous field use.
- A live conversation captured in full (record **#60435**, 3.68 MB `POST /v1/messages`) and
  verified within the chain.
- Tamper-detection self-test: an untampered chain verifies; a single-byte change to a past
  record is detected at the exact entry.

See [`docs/TIGGR_Audit_WhitePaper.html`](docs/TIGGR_Audit_WhitePaper.html) and
[`docs/Capture_Validation.md`](docs/Capture_Validation.md).

## What's in this repository
```
docs/      White paper (technical) + field-capture validation report
samples/   Example cryptographic ledger (opaque hashes + metadata; no content)
```
- [`samples/cryptographic_ledger.sample.csv`](samples/cryptographic_ledger.sample.csv) — an
  illustrative ledger. It is internally chain-consistent (each row's `prev_chain_hash`
  matches the previous row's `chain_hash`), and the zero-byte requests carry the real
  SHA-256 of an empty payload (`e3b0c442…b855`) — verifiable by anyone.

## How to evaluate (for reviewers)
1. **Open the live demo** above — confirm the **CHAIN VERIFIED** banner and the metrics.
2. **Inspect the sample ledger** — note that every record links to the previous via its
   chain hash; changing any field would break that continuity.
3. **Read the white paper** for the architecture, the validation evidence, and scope.

## Status & IP
- **Patent pending.** The TIGGR capture engine and the chain-construction protocol are
  **proprietary** and are **not** included here (see [`NOTICE.md`](NOTICE.md)). This repository
  is a documentation + validated-output + live-demo showcase. Engine source is available
  under evaluation agreement.
- No customer prompt content is included anywhere in this repository.

## About
**Porritt Inc.** — Layton, UT · SAM-registered (UEI WQWHVJ1J98S9, CAGE 9Z7B9) ·
audit-grade AI development. Contact: timothy@porrittinc.com
