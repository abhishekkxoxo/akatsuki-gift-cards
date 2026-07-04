# Akatsuki 🥷 — Gift Card Vendor Integration Agent

A Claude Code custom slash command that guides you through integrating a new digital gift card / voucher vendor into the Xoxoday platform end-to-end.

## What it does

- Accepts API docs (Postman collection, OpenAPI JSON, or raw text) and pre-fills answers, or walks you through Q&A if you have nothing to paste.
- Asks structured batches of questions — vendor identity, auth, order placement, reconciliation, product catalog, balance reporting.
- Runs a feasibility check and waits for your approval before writing anything.
- Confirms your Jira branch before touching any file.
- Writes all required files automatically:

| File | Action |
|------|--------|
| `vendor-integrations/src/vendors/integratedVendors/{VendorName}.ts` | Created |
| `vendor-integrations/src/vendors/vendorTemplates/{vendorName}.ts` | Created (if complex types needed) |
| `vendor-integrations/src/configs/config.json` | Updated (`vendorConfigs` entry + `vendorClassIdMapping`) |
| `karma_reports/vendorBalance/vendors/{vendorName}Balance.js` | Created (only if vendor has a balance endpoint) |
| `karma_reports/vendorBalance/vendorBalance.js` | Updated (only if vendor has a balance endpoint) |
| `karma_reports/conf.json.sample` | Updated (only if vendor has a balance endpoint) |

## What you still do manually after it finishes

- Fill real credentials in `config.json` (replace placeholders)
- Add `vendorId` to `order-process-ms/config_voucher_process.json` → `integratedVendors` array
- Run `cd vendor-integrations && npm run merge-configs && npx tsc`
- Add vendor product pages, balance display row, and menu links in the `karma/` PHP admin panel
- Test order placement, reconciliation, and product sync before going live

## Installation

Copy `akatsuki-giftcards.md` into your project's `.claude/commands/` directory:

```bash
cp akatsuki-giftcards.md /path/to/your/project/.claude/commands/
```

Then use it in Claude Code:

```
/akatsuki-giftcards
```

Drop your vendor's API docs when prompted, or type `start` to begin Q&A.

## Scope

This agent handles **digital voucher codes only** — a code (and sometimes a PIN) with a validity/expiry, at fixed denominations or variable open amounts. It does **not** handle physical merchandise, shipping, couriers, or delivery tracking. For merchandise vendors, see [merch-sensei](https://github.com/abhishekkxoxo/merch-sensei).
