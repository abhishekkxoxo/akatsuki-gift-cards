# Akatsuki 🥷 — Gift Card Vendor Integration Agent

A Claude Code slash command for digital gift card / voucher vendors on the Xoxoday platform. It has three modes:

| Mode | Use it to |
|---|---|
| **Onboard** | Integrate a new gift card vendor: repo preflight → question batches → feasibility → branch check → code → tests → handoff |
| **Debug** | Walk an order through nine hops (OPMS → VI validation → product/SKU → auth → vendor call → storage → recon → sync → balance) and stop at the failing one (read-only) |
| **Test** | Vendor API contract tests with the Postman CLI first, then Jest unit tests of our connector |


Before any feasibility verdict it asks for **staging credentials** and runs **every vendor API** live with the Postman CLI. It only calls an integration 100% feasible when every endpoint passed. It writes the vendor config into the repo with the staging URL and credentials. That file is gitignored, so it stays local; you swap in production values. If the vendor has a balance endpoint, it also writes the karma_reports balance module.

## What it writes

| File | Where | In the PR? |
|---|---|---|
| `src/vendors/integratedVendors/{vendorFile}.ts` | vendor-integrations | Yes |
| `src/vendors/vendorTemplates/{vendorName}.ts` (if needed) | vendor-integrations | Yes |
| `src/configs/baseConfigs.json.sample` (`vendorClassIdMapping` line) | vendor-integrations | Yes |
| `tests/vendors/{vendorName}.*.test.ts` + fixtures | vendor-integrations | Yes |
| `src/configs/vendorConfigs/{VendorName}.json` (staging values) + local `baseConfigs.json` mapping | vendor-integrations | No: gitignored, stays local |
| `config.php.sample`, `vendor_tables.php`, `sale/{key}_voucher` listing page (controller, model, language, tpl), `header.tpl`, `product_automation.tpl`; for fixedSku also the product controller/model and both product form templates; `order_list.tpl` if there is a balance | karma | Yes |
| `vendorBalance/vendors/{vendorName}Balance.js`, `vendorBalance.js`, `conf.json.sample` | karma_reports | Yes, only if the vendor has a balance endpoint |

It never hand-edits `config.json`, which `npm run merge-configs` regenerates (`npm run dev` and `prestart` run it too).

## What still needs doing outside this command

The handoff gives a per-repo, per-env checklist:
- VI deployment config and token scheduler;
- OPMS `vendorIntegration.integratedVendors` (string ids);
- the `realtimeVendorModels` lists in product-listing and plum_client_order;
- `vendor` / `vendor_to_model` rows and the products table DDL;
- the real `karma/config.php` define in every env before deploy, and the User Group permission;
- the sync order and a smoke test.

## Installation

```bash
cp akatsuki-giftcards.md /path/to/your/project/.claude/commands/
```

Then in Claude Code:

```
/akatsuki-giftcards
```

Drop your vendor's API docs, say `start`, or say `debug {vendorId} {orderId|VID|symptom}` or `test`.

## Scope

Digital voucher codes only: a code (and sometimes a PIN) with a validity, at fixed or variable denominations. For merchandise vendors, see [merch-sensei](https://github.com/abhishekkxoxo/merch-sensei). For mobile topup / recharge vendors, see [topup-sensei](https://github.com/abhishekkxoxo/topup-sensei).
