# ZATCA e-invoice validation rules — machine-readable (135 codes)

`zatca-validation-rules.json` / `.csv` list every validation rule ZATCA's e-invoicing platform applies to a Phase 2 (Fatoora) invoice, with the **official English message text verbatim** and its severity:

- `error` — the invoice is **rejected** (`BR-KSA-26`, `BR-KSA-33`, `BR-KSA-F-04`, …)
- `warning` — the invoice is **accepted with warnings** (`BR-KSA-EN16931-09`, …)

Each entry links to a guide with the cause and the fix (Arabic, at zatcatools.com/docs/errors). Twenty codes have a dedicated page; the rest anchor into the full index.

**Source and licence.** The rules were extracted from the compiled schematron shipped inside ZATCA's own e-invoicing SDK (`Data/Rules/schematrons/20210819_ZATCA_E-invoice_Validation_Rules.xsl`, SDK Java 238 R3.3.9, GNU LGPL v3). Messages are ZATCA's text and were not edited. Note that the live API validates a little more than the published schematron — `BR-KSA-F-13` was captured from a real production response and is included.

```json
{
  "code": "BR-KSA-26",
  "severity": "error",
  "message": "The previous invoice hash (KSA-13) must be a SHA-256 hash encoded in Base64 …",
  "guide": "https://zatcatools.com/docs/errors/br-ksa-26"
}
```

Typical uses: map API rejections to human-readable help in your own UI, build test fixtures, or grep a code you just received. For the twenty most common codes with one-line fixes, see [`../docs/zatca-error-codes-cheatsheet.md`](../docs/zatca-error-codes-cheatsheet.md).
