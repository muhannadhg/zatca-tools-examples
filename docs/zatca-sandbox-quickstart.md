# ZATCA (Fatoora) sandbox quickstart — test Phase 2 e-invoicing with no credentials

You do **not** need anything from ZATCA to start integrating. The developer sandbox accepts any correctly formed CSR with a fixed OTP, and validates invoices for a published test VAT number. This page is the shortest path from zero to an accepted invoice.

## The three environments

| Environment | Base URL | OTP | Certificate template in the CSR |
|---|---|---|---|
| **Sandbox** (developer portal) | `https://gw-fatoora.zatca.gov.sa/e-invoicing/developer-portal` | fixed: `123345` | `TSTZATCA-Code-Signing` |
| **Simulation** | `https://gw-fatoora.zatca.gov.sa/e-invoicing/simulation` | from the Fatoora portal (simulation) | `PREZATCA-Code-Signing` |
| **Production** | `https://gw-fatoora.zatca.gov.sa/e-invoicing/core` | from the Fatoora portal (production) | `ZATCA-Code-Signing` |

Two facts that save days:

- **The sandbox validates invoices only for the test VAT `399999999900003`.** A CSR with a real VAT number will onboard, but its invoices come back rejected. Use the test VAT for the whole sandbox run.
- **An OTP belongs to one environment.** A code issued in the simulation portal does not work in production and vice versa. In production the code is single-use and expires after one hour, so generate it last — with your establishment data ready and the onboarding screen open.

## The onboarding flow (identical in all three environments)

1. **Key + CSR.** Generate a `secp256k1` EC key and a CSR whose subject carries your organisation (`CN`, `O`, `OU`, `C=SA`) and whose extensions carry the ZATCA fields: serial number in the form `1-<solution>|2-<model>|3-<uuid>`, `UID` = VAT number, `title` = invoice types you will issue (`1100` = standard + simplified, `0100` = standard only, `1000` = simplified only), the registered address and the business category — plus the certificate template name from the table above.
2. **Compliance CSID.** `POST {base}/compliance` with headers `OTP: 123345` (sandbox) and `Accept-Version: V2`, body `{"csr": "<base64 of the PEM CSR>"}`. The response carries `binarySecurityToken`, `secret` and `requestID`.
3. **Six compliance documents.** With HTTP Basic auth `base64(binarySecurityToken:secret)`, send one of each document type your CSR declared to `POST {base}/compliance/invoices`: standard invoice, standard credit note, standard debit note, simplified invoice, simplified credit note, simplified debit note. Each must come back `REPORTED`/`CLEARED` (warnings are allowed; errors are not).
4. **Production CSID.** `POST {base}/production/csids` with body `{"compliance_request_id": "<requestID from step 2>"}` using the compliance credentials. The response is the certificate you sign real invoices with. Renewal later is `PATCH {base}/production/csids` with a fresh CSR and a new OTP.
5. **Issue.** Simplified (B2C) invoices go to `POST {base}/invoices/reporting/single` with header `Clearance-Status: 0`; standard (B2B) invoices go to `POST {base}/invoices/clearance/single` with `Clearance-Status: 1`. Body: `{"invoiceHash": "...", "uuid": "...", "invoice": "<base64 signed UBL XML>"}`.

## Values people get wrong on the first run

- **ICV** (`KSA-16`) is a counter that starts at `1` and only ever increases — it is not the invoice number and it never resets per year or per device.
- **PIH** (`KSA-13`) is the base64 of the SHA-256 of the *previous* invoice's XML. For the very first invoice it is the hash of the string `0`: `NWZlY2ViNjZmZmM4NmYzOGQ5NTI3ODZjNmQ2OTZjNzljMmRiYzIzOWRkNGU5MWI0NjcyOWQ3M2EyN2ZiNTdlOQ==`.
- **Dates** are validated against ZATCA's clock in Riyadh time — a server on UTC issuing after 21:00 local produces a "future" date and `BR-KSA-04`.
- **Postal code** is exactly five digits (`BR-KSA-66`); the 4-digit "additional number" is not it. **District** (`CitySubdivisionName`) is mandatory (`BR-KSA-09`).
- **Line amounts** are positive; returns are credit notes (`381`) that reference the original invoice and carry a reason, not negative lines (`BR-KSA-F-04`, `BR-KSA-56`, `BR-KSA-17`).

The full list of rejection codes with the official message text and a fix for each: [ZATCA error codes cheat sheet](zatca-error-codes-cheatsheet.md).

## If you would rather not run this layer yourself

ZATCA Tools runs steps 1–5 as a hosted service: your system posts invoice JSON, the service builds the UBL, signs it (XAdES), maintains the ICV/PIH chain, reports or clears with ZATCA and returns the QR, XML and PDF. There is a trial mode against this same sandbox with no VAT number and no OTP, and the first 50 production invoices are free.

- API reference: https://zatcatools.com/docs/api?utm_source=github&utm_medium=docs&utm_campaign=sandbox-quickstart
- Sign up (no card): https://zatcatools.com/start?utm_source=github&utm_medium=docs&utm_campaign=sandbox-quickstart

Examples in cURL, PHP, Node.js and Python are in [`../examples/`](../examples/).
