<p align="center">
  <a href="https://zatcatools.com"><img src="assets/banner.png" alt="ZATCA Tools — Saudi ZATCA e-Invoicing Examples & Free Tools" width="100%"></a>
</p>

# Saudi ZATCA e-Invoicing — Examples & Free Tools

Ready-to-run integration examples and free developer tools for **Saudi Arabia's e-invoicing (ZATCA Phase 2 — Fatoora)**.

Brought to you by [**ZATCA Tools**](https://zatcatools.com) — the fastest way for Saudi businesses to connect to ZATCA and issue compliant, cryptographically-signed e-invoices in minutes. No accounting system required. **Free — no card required.**

| | |
|---|---|
| 🌐 App | [zatcatools.com](https://zatcatools.com) |
| 📚 API docs | [zatcatools.com/docs/api](https://zatcatools.com/docs/api?utm_source=github&utm_medium=readme&utm_campaign=header) |
| 🧪 Sandbox quickstart (no credentials needed) | [docs/zatca-sandbox-quickstart.md](docs/zatca-sandbox-quickstart.md) |
| 🚫 Rejection codes cheat sheet | [docs/zatca-error-codes-cheatsheet.md](docs/zatca-error-codes-cheatsheet.md) |
| 🗂️ Validation rules dataset (135 codes, JSON/CSV) | [data/](data/) |
| ✍️ Blog (Arabic guides) | [zatcatools.com/blog](https://zatcatools.com/blog) |

---

## Quick start

Issue a ZATCA-compliant invoice in one API call — the platform handles the hard parts (UBL XML, XAdES digital signature, TLV QR code, ICV/PIH chain, clearance/reporting with ZATCA) and returns the accepted invoice with ready PDF/XML links:

```bash
curl -X POST https://zatcatools.com/api/v1/invoices \
  -H "Authorization: Bearer ztk_live_YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{"type":"simplified","lines":[{"name":"Consulting service","quantity":1,"unit_price":500}]}'
```

> 🔑 **Getting an API key**: [sign up free](https://zatcatools.com/start?utm_source=github&utm_medium=readme&utm_campaign=quickstart) → complete Fatoora onboarding → Settings → API tab → *Generate key*.

## 📦 What's inside

### 1) API integration examples — [`examples/`](examples/)

Issue an accepted tax invoice in under 30 lines, with zero external dependencies:

| Language | File |
|---|---|
| cURL (all endpoints) | [`examples/curl/README.md`](examples/curl/README.md) |
| PHP | [`examples/php/issue-invoice.php`](examples/php/issue-invoice.php) |
| Node.js 18+ | [`examples/node/issue-invoice.mjs`](examples/node/issue-invoice.mjs) |
| Python 3 | [`examples/python/issue_invoice.py`](examples/python/issue_invoice.py) |

Covered: simplified (B2C) & standard (B2B) invoices, credit/debit notes, listing with filters, and downloading the signed **PDF / UBL XML / QR**.

### 2) Free developer tools — [`tools/`](tools/)

Standalone utilities, useful even if you don't use our platform:

| Tool | What it does |
|---|---|
| [`qr-tlv-decoder`](tools/qr-tlv-decoder/) | Decode any Saudi invoice QR code (base64 TLV → readable fields, Phase 1 & 2 tags) |
| [`qr-tlv-generator`](tools/qr-tlv-generator/) | Generate a Phase-1-compliant TLV QR payload |
| [`vat-validator`](tools/vat-validator/) | Validate a Saudi VAT registration number (15 digits, format rules) |

## Common questions (short answers)

**Do I need anything from ZATCA to start testing?** No. The developer sandbox accepts any well-formed CSR with the fixed OTP `123345` and validates invoices for the test VAT `399999999900003`. Step-by-step: [docs/zatca-sandbox-quickstart.md](docs/zatca-sandbox-quickstart.md).

**Sandbox vs simulation vs production?** Three base URLs under `gw-fatoora.zatca.gov.sa/e-invoicing/` — `developer-portal`, `simulation`, `core` — with three CSR template names and separate OTPs. An OTP is valid in one environment only, once, for one hour.

**Is there a free ZATCA e-invoicing API?** Yes: [ZATCA Tools](https://zatcatools.com/docs/api?utm_source=github&utm_medium=readme&utm_campaign=faq) signs (XAdES), chains (ICV/PIH), reports/clears and returns QR + XML + PDF from one JSON call. First 50 invoices free, no card; a trial mode runs on ZATCA's sandbox with no VAT number or OTP.

**Which rejection codes are most common?** `BR-KSA-26`/`61` (PIH), `BR-KSA-33` (ICV), `BR-KSA-09`/`66` (address and postal code), `BR-KSA-04` (timezone), `BR-KSA-F-04` (negative lines), `BR-KSA-EN16931-09` (tax total with a tax currency). One-line fixes for twenty codes: [docs/zatca-error-codes-cheatsheet.md](docs/zatca-error-codes-cheatsheet.md).

**Does it plug into an existing store or workflow?** Yes — [WooCommerce plugin](https://zatcatools.com/docs/woocommerce?utm_source=github&utm_medium=readme&utm_campaign=faq), [Shopify app](https://zatcatools.com/shopify?utm_source=github&utm_medium=readme&utm_campaign=faq), [n8n community node](https://zatcatools.com/tools/n8n?utm_source=github&utm_medium=readme&utm_campaign=faq), and the REST API for anything else.

## Why ZATCA Tools?

- ⚡ **Onboarding in minutes** — CSR, compliance CSID, the 6 compliance checks, and production CSID are fully automated
- 🧾 **Standard (B2B) & simplified (B2C) invoices** — signed, cleared/reported, with credit & debit notes
- 🔗 **Clean REST API** — JSON in, accepted invoice + PDF out; uniform `{error:{code,message}}` errors
- 🗄️ **XML/PDF archive** of every invoice in its official format
- 🆓 **Free** — no card required

## Contributing

Found a bug, or want to add an example in another language (C#, Go, Java…)? Issues and PRs are welcome 🤝

## License

[MIT](LICENSE) — use freely in your own projects.

---

<div dir="rtl">

## ملخص بالعربي

هذا المستودع يقدّم أمثلة تكامل جاهزة (cURL / PHP / Node.js / Python) وأدوات مجانية (فك وتوليد رمز QR بصيغة TLV، والتحقق من الرقم الضريبي) للفوترة الإلكترونية السعودية — المرحلة الثانية (فاتورة). مقدّم من [ZATCA Tools](https://zatcatools.com): اربط منشأتك مع هيئة الزكاة وأصدر فواتير معتمدة خلال دقائق — مجانًا وبدون بطاقة. التوثيق الكامل بالعربي: [docs/api](https://zatcatools.com/docs/api).

</div>
