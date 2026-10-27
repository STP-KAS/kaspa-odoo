# Kaspa and Odoo

Independent write-up, 27 Oct 2026. Not affiliated with Odoo SA or the Kaspa developers. Each statement links to its source.

> **Disclaimer.** The author is not a Kaspa contributor and does not take part in Kaspa development in any way. They are an interested individual who tests on Kaspa's testnet. Kaspa is open source ([official Kaspa GitHub organization](https://github.com/kaspanet)) and decentralized: anyone can build on it.

**Why this exists.** The author's brother runs a small restaurant company that is an Odoo customer. That is why the author wanted to look at how Kaspa could potentially help Odoo.

## Summary

Odoo is a Belgian, open-core suite of business applications ([about](https://www.odoo.com/page/about-us)). Kaspa is a proof-of-work blockDAG producing 10 blocks per second on mainnet ([KIP-14](https://github.com/kaspanet/kips/blob/master/kip-0014.md), [kaspa.org/lore](https://kaspa.org/lore)). Odoo's community code (branches 18.0, 19.0, 20.0) contains no Kaspa integration and no crypto-asset payment provider. Odoo already handles invoicing, journal inalterability, traceability and e-invoicing locally; Kaspa could add an external payment rail and independent public timestamps. KAS uses 8 decimals ([`SOMPI_PER_KASPA`](https://github.com/kaspanet/rusty-kaspa/blob/master/consensus/core/src/constants.rs)), which Odoo's 6-digit currency rounding cannot represent. Any integration is best evaluated and provided by Odoo itself.

Kaspa core: [github.com/kaspanet](https://github.com/kaspanet). Reliable sources: [kaspa.org](https://kaspa.org), [docs.kaspa.org](https://docs.kaspa.org), [research.kas.pa](https://research.kas.pa), [qa.kas.pa](https://qa.kas.pa/), [Kaspa Discord](https://discord.gg/kaspa), [t.me/kasparnd](https://t.me/kasparnd), [kaspa.news](https://kaspa.news), [Kaspa Explained](https://kaspaexplained.com/status).

## Odoo

### Company and editions
| Item | Fact | Source |
|---|---|---|
| Company | Odoo SA, headquartered in Belgium (Louvain-la-Neuve, Antwerp, Grand-Rosière offices); founder and CEO Fabien Pinckaers | [about](https://www.odoo.com/page/about-us) |
| Users | Over 28,000,000 users, from 1-user startups to enterprises with 300,000+ users | [about](https://www.odoo.com/page/about-us) |
| Community edition | Open source, LGPL-3 | [github.com/odoo/odoo](https://github.com/odoo/odoo), [LICENSE](https://github.com/odoo/odoo/blob/19.0/LICENSE) |
| Enterprise edition | Paid; adds apps such as Sign and Documents | [editions page](https://www.odoo.com/page/editions) |
| Extensions | Third-party modules via the Apps store | [apps.odoo.com](https://apps.odoo.com) |

### What it does
Odoo is a modular ERP: each business function is an app sharing one database, so a sale can drive stock, invoicing and accounting without re-entry ([about](https://www.odoo.com/page/about-us)).

| Area | Community modules (19.0) |
|---|---|
| Accounting and invoicing | [`account`](https://github.com/odoo/odoo/tree/19.0/addons/account), [`account_peppol`](https://github.com/odoo/odoo/tree/19.0/addons/account_peppol) |
| Sales and CRM | [`sale`](https://github.com/odoo/odoo/tree/19.0/addons/sale), [`crm`](https://github.com/odoo/odoo/tree/19.0/addons/crm) |
| Inventory and manufacturing | [`stock`](https://github.com/odoo/odoo/tree/19.0/addons/stock), [`mrp`](https://github.com/odoo/odoo/tree/19.0/addons/mrp) |
| Point of sale | [`point_of_sale`](https://github.com/odoo/odoo/tree/19.0/addons/point_of_sale) |
| E-commerce | [`website_sale`](https://github.com/odoo/odoo/tree/19.0/addons/website_sale) |
| HR | [`hr`](https://github.com/odoo/odoo/tree/19.0/addons/hr) |
| Payments | [`payment`](https://github.com/odoo/odoo/tree/19.0/addons/payment) plus `payment_*` providers |

Odoo's about page gives its user range (see above); it does not break customers down by sector.

## Gaps and possible solutions

Status is binary: **Gap** (no community module covers it) or **Handled by Odoo**. Every Kaspa approach below is a possible design; none is implemented.

### 1. Accepting KAS payments — Gap
- **Odoo today:** the [`payment`](https://github.com/odoo/odoo/tree/19.0/addons/payment) framework with 23 `payment_*` providers on 19.0 (17 on 18.0), none crypto ([addons](https://github.com/odoo/odoo/tree/19.0/addons/)). Third-party crypto providers exist on the Apps store, e.g. [BTCPay Server](https://apps.odoo.com/apps/modules/17.0/payment_btcpayserver), [OpenNode](https://apps.odoo.com/apps/modules/19.0/payment_opennode); none for Kaspa.
- **Gap:** no KAS provider.
- **Possible solution:** a `payment_kaspa` module modelled on [`payment_custom`](https://github.com/odoo/odoo/blob/19.0/addons/payment_custom/__manifest__.py) (show a per-order address and amount, then confirm the transaction when a node or indexer reports it accepted). Fast block production ([KIP-14](https://github.com/kaspanet/kips/blob/master/kip-0014.md)) shortens the wait between payment and confirmation in POS and checkout.

### 2. KAS amounts and currency rounding — Handled by Odoo (fiat) / Gap (KAS)
- **Odoo today:** `rounding` stored with `digits=(12, 6)` in [`res_currency.py`](https://github.com/odoo/odoo/blob/19.0/odoo/addons/base/models/res_currency.py).
- **Gap:** 1 KAS = 10^8 sompi ([`SOMPI_PER_KASPA`](https://github.com/kaspanet/rusty-kaspa/blob/master/consensus/core/src/constants.rs)); 6 decimals cannot hold sompi precision.
- **Possible solution:** book in KAS rounded to 6 decimals and store the exact sompi amount on the payment transaction; or treat sompi as the integer unit. Reconciliation would match the on-chain amount to the invoice via [`account_reconcile_model.py`](https://github.com/odoo/odoo/blob/19.0/addons/account/models/account_reconcile_model.py).

### 3. Journal inalterability — Handled by Odoo
- **Odoo today:** `inalterable_hash` and `secure_sequence_number` on [`account_move.py`](https://github.com/odoo/odoo/blob/19.0/addons/account/models/account_move.py); `restrict_mode_hash_table` on [`account_journal.py`](https://github.com/odoo/odoo/blob/19.0/addons/account/models/account_journal.py); French certification in [`l10n_fr_account`](https://github.com/odoo/odoo/tree/19.0/addons/l10n_fr_account).
- **Gap:** the hash chain lives only in the Odoo database.
- **Possible solution:** periodically publish the latest chain hash in a Kaspa transaction payload. An auditor can then check that the chain existed at that time without trusting the database operator.

### 4. Lot / serial traceability — Handled by Odoo
- **Odoo today:** [`stock_lot.py`](https://github.com/odoo/odoo/blob/19.0/addons/stock/models/stock_lot.py), [`stock_traceability.py`](https://github.com/odoo/odoo/blob/19.0/addons/stock/report/stock_traceability.py), [`mrp`](https://github.com/odoo/odoo/tree/19.0/addons/mrp).
- **Gap:** traceability is visible only inside one company's database.
- **Possible solution:** anchor a hash (or Merkle root) of lot movements per batch; share the lot record off-chain so partners can verify it against the anchor. [KCC-20](https://github.com/kaspanet/kccs/blob/main/kcc-0020.md) tokens could represent batches, if a business wants transferable records.

### 5. EU e-invoicing / Peppol — Handled by Odoo
- **Odoo today:** [`account_edi_ubl_cii`](https://github.com/odoo/odoo/tree/19.0/addons/account_edi_ubl_cii), [`account_peppol`](https://github.com/odoo/odoo/tree/19.0/addons/account_peppol).
- **Gap:** no independent timestamp outside the Peppol network.
- **Possible solution:** anchor the UBL document hash as optional evidence. Peppol remains the regulated channel.

### 6. Transfers between subsidiaries — Gap
- **Odoo today:** no `inter_company` addon in community (18.0–20.0); the [multi-company doc](https://www.odoo.com/documentation/19.0/applications/general/companies/multi_company.html) describes inter-company transactions.
- **Gap:** no on-chain settlement option.
- **Possible solution:** settle intercompany balances in KAS and post matching entries in both companies; covenants ([KIP-17](https://github.com/kaspanet/kips/blob/master/kip-0017.md), active since Toccata, [kaspa.org/lore](https://kaspa.org/lore)) could enforce conditional or time-locked releases.

### 7. Document signing / notarization — Gap
- **Odoo today:** Sign and Documents are Enterprise-only ([editions page](https://www.odoo.com/page/editions)); no community addon.
- **Gap:** no community signing or notarization.
- **Possible solution:** hash a signed PDF and anchor the hash; anyone with the document can verify the timestamp. This proves existence, not signer identity.

### 8. Point of sale and e-commerce — Gap (KAS)
- **Odoo today:** [`point_of_sale`](https://github.com/odoo/odoo/tree/19.0/addons/point_of_sale) and [`website_sale`](https://github.com/odoo/odoo/tree/19.0/addons/website_sale) use the payment provider framework.
- **Gap:** inherits gap 1.
- **Possible solution:** a QR code with address and amount on the POS screen or checkout page, confirmed by the same `payment_kaspa` module; immediate conversion to fiat would be a business choice.

### 9. Escrow and conditional payments — Gap
- **Odoo today:** no escrow module in community.
- **Possible solution:** covenant scripts ([KIP-17](https://github.com/kaspanet/kips/blob/master/kip-0017.md)) holding funds until delivery is confirmed.

## How Kaspa could help Odoo and its customers

- **For Odoo as a vendor:** a maintained KAS payment provider and an optional anchoring service for the existing hash chain, traceability and documents, alongside current providers.
- **For Odoo customers:** Odoo is extensible, so a customer or partner can build or install a custom or third-party module on their own initiative ([apps.odoo.com](https://apps.odoo.com)).
- **Note:** given the accounting, tax and compliance implications, it is best for Odoo itself to look into this and, if it fits, provide it to its customers.

## Audit (Grok)

Checked on 27 Oct 2026 against primary sources: the [odoo/odoo](https://github.com/odoo/odoo) source (branches 18.0, 19.0, 20.0 via the GitHub API), [odoo.com](https://www.odoo.com) pages and docs, [apps.odoo.com](https://apps.odoo.com), the [kaspanet](https://github.com/kaspanet) KIPs, KCCs and rusty-kaspa repos, and [kaspa.org](https://kaspa.org)/[kaspa.org/lore](https://kaspa.org/lore). Every linked URL was fetched; claims that could not be matched to a source were left out.

## Quick reference: reliable sources

| Topic | Links |
|---|---|
| Kaspa official | [kaspa.org](https://kaspa.org) · [kaspa.org/lore](https://kaspa.org/lore) · [github.com/kaspanet](https://github.com/kaspanet) · [rusty-kaspa](https://github.com/kaspanet/rusty-kaspa) · [KIPs](https://github.com/kaspanet/kips) · [KCCs](https://github.com/kaspanet/kccs) |
| Docs and research | [docs.kaspa.org](https://docs.kaspa.org) · [wiki.kaspa.org](https://wiki.kaspa.org) · [research.kas.pa](https://research.kas.pa) · [Kaspa Explained](https://kaspaexplained.com/status) · [Kaspa master file](https://github.com/STP-KAS/kaspa-master-file) |
| Community | [Kaspa Discord](https://discord.gg/kaspa) · [qa.kas.pa](https://qa.kas.pa/) · [t.me/kasparnd](https://t.me/kasparnd) · [kaspa.news](https://kaspa.news) |
| Explorer | [kaspa.stream](https://kaspa.stream) |
| Odoo | [odoo.com](https://www.odoo.com) · [editions page](https://www.odoo.com/page/editions) · [apps.odoo.com](https://apps.odoo.com) · [payment providers docs](https://www.odoo.com/documentation/19.0/applications/finance/payment_providers.html) · [github.com/odoo/odoo](https://github.com/odoo/odoo) |

This document contains no price information or financial advice.
