# ZATCA e-invoice rejection codes — cheat sheet

The codes ZATCA's API returns (`BR-KSA-*`, `BR-KSA-F-*`, `BR-KSA-EN16931-*`, `BR-CUSTOM-VALIDATION-*`) come from the validation rules published in ZATCA's own SDK. There are 135 of them; the twenty below account for most real-world rejections. Each row links to a longer guide (Arabic, with the official message text and the exact XML shape).

| Code | What it means | Fix |
|---|---|---|
| [BR-KSA-26](https://zatcatools.com/docs/errors/br-ksa-26?utm_source=github&utm_medium=docs&utm_campaign=cheatsheet) | `PIH` (KSA-13) is not the base64 of a SHA-256 hash | Hash the *previous* invoice's XML with SHA-256, base64 the hash; the first invoice uses the hash of `0` |
| [BR-KSA-61](https://zatcatools.com/docs/errors/br-ksa-61?utm_source=github&utm_medium=docs&utm_campaign=cheatsheet) | `PIH` element missing entirely | Add the `KSA-13` additional document reference — 26 is a wrong value, 61 is no value |
| [BR-KSA-33](https://zatcatools.com/docs/errors/br-ksa-33?utm_source=github&utm_medium=docs&utm_campaign=cheatsheet) | `ICV` (KSA-16) is not a strictly increasing counter | Keep one counter per certificate that starts at 1 and never resets or repeats — it is not the invoice number |
| [BR-CUSTOM-VALIDATION-01](https://zatcatools.com/docs/errors/br-custom-validation-01?utm_source=github&utm_medium=docs&utm_campaign=cheatsheet) | Seller VAT equals buyer VAT | Never invoice your own VAT number; internal transfers are not tax invoices |
| [BR-KSA-40](https://zatcatools.com/docs/errors/br-ksa-40?utm_source=github&utm_medium=docs&utm_campaign=cheatsheet) | Seller VAT format invalid | 15 ASCII digits starting and ending with `3` — not the unified number, not the CR |
| [BR-KSA-39](https://zatcatools.com/docs/errors/br-ksa-39?utm_source=github&utm_medium=docs&utm_campaign=cheatsheet) | Seller VAT (or VAT-group number) absent | Put the seller VAT in `PartyTaxScheme/CompanyID` |
| [BR-KSA-44](https://zatcatools.com/docs/errors/br-ksa-44?utm_source=github&utm_medium=docs&utm_campaign=cheatsheet) | Buyer VAT present/absent in the wrong document | Standard (B2B) invoice: buyer VAT required for VAT-registered buyers; simplified (B2C): leave it out |
| [BR-KSA-F-13](https://zatcatools.com/docs/errors/br-ksa-f-13?utm_source=github&utm_medium=docs&utm_campaign=cheatsheet) | Other-ID (BT-29 / BT-46) value does not match its declared scheme | Match `schemeID` (`CRN`, `MOM`, `MLS`, `700`, `SAG`, `NAT`, `GCC`, `IQA`, `PAS`, `OTH`) to the actual identifier |
| [BR-KSA-09](https://zatcatools.com/docs/errors/br-ksa-09?utm_source=github&utm_medium=docs&utm_campaign=cheatsheet) | Seller address incomplete | Street, building number, additional number/plot, **district**, city, **postal code** — the last two are what people miss |
| [BR-KSA-66](https://zatcatools.com/docs/errors/br-ksa-66?utm_source=github&utm_medium=docs&utm_campaign=cheatsheet) | Seller postal code not 5 digits | Postal code is 5 digits; the 4-digit "additional number" goes in `PlotIdentification` |
| [BR-KSA-67](https://zatcatools.com/docs/errors/br-ksa-67?utm_source=github&utm_medium=docs&utm_campaign=cheatsheet) | Buyer postal code not 5 digits (B2B) | Same rule on the buyer side; only standard invoices carry a full buyer address |
| [BR-KSA-06](https://zatcatools.com/docs/errors/br-ksa-06?utm_source=github&utm_medium=docs&utm_campaign=cheatsheet) | Invoice type transaction code malformed | 7 characters in `InvoiceTypeCode/@name`: `01…` standard, `02…` simplified, then the five transaction flags |
| [BR-KSA-05](https://zatcatools.com/docs/errors/br-ksa-05?utm_source=github&utm_medium=docs&utm_campaign=cheatsheet) | Invoice type code not allowed | Only `388` invoice, `381` credit note, `383` debit note — there is no "cancel" |
| [BR-KSA-56](https://zatcatools.com/docs/errors/br-ksa-56?utm_source=github&utm_medium=docs&utm_campaign=cheatsheet) | Credit/debit note without a reference to the original invoice | Add `BillingReference/InvoiceDocumentReference/ID` = original invoice number |
| [BR-KSA-17](https://zatcatools.com/docs/errors/br-ksa-17?utm_source=github&utm_medium=docs&utm_campaign=cheatsheet) | Credit/debit note without a reason | Add the reason text in `PaymentMeans/InstructionNote` |
| [BR-KSA-04](https://zatcatools.com/docs/errors/br-ksa-04?utm_source=github&utm_medium=docs&utm_campaign=cheatsheet) | Issue date in the future | Compute the issue date in Riyadh time (UTC+3), not server UTC — the classic 21:00–24:00 bug |
| [BR-KSA-18](https://zatcatools.com/docs/errors/br-ksa-18?utm_source=github&utm_medium=docs&utm_campaign=cheatsheet) | Line VAT category invalid | One letter only: `S` standard, `Z` zero-rated, `E` exempt, `O` out of scope — never the rate |
| [BR-KSA-F-04](https://zatcatools.com/docs/errors/br-ksa-f-04?utm_source=github&utm_medium=docs&utm_campaign=cheatsheet) | Negative amount or quantity | Amounts are positive; model returns as a credit note, not as a negative line |
| [BR-KSA-EN16931-09](https://zatcatools.com/docs/errors/br-ksa-en16931-09?utm_source=github&utm_medium=docs&utm_campaign=cheatsheet) | `TaxCurrencyCode` declared but the subtotal-free `TaxTotal` is missing or duplicated | Emit exactly one `TaxTotal` **without** `TaxSubtotal` (the SAR tax amount) in addition to the detailed one — or drop `TaxCurrencyCode` when it equals the document currency |

Full index of all 135 codes with search: https://zatcatools.com/docs/errors?utm_source=github&utm_medium=docs&utm_campaign=cheatsheet

Note: ZATCA's live API validates slightly more than the published SDK — `BR-KSA-F-13` is one such code that is absent from the SDK build and was captured from a real response.

To get to an accepted invoice from scratch, see the [sandbox quickstart](zatca-sandbox-quickstart.md).
