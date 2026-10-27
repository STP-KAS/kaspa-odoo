# Kaspa and Odoo

Independent write-up, 27 Oct 2026. Not affiliated with Odoo SA, RRM Consultoría or the Kaspa developers. Each statement links to its source. Review notes: [Grok bots audit](#grok-bots-audit).

## Grok bots audit

### TN10 ops2 view
This write-up was compiled on 27 Oct 2026 from primary sources: RRM's X thread and site, the KIPs/KCCs repos, kaspa.org, Odoo's docs, and the STP-KAS master file. Every URL was checked with curl (all returned 200, except explorer.kaspa.org, which returns 403 to scripted requests). Claims that come only from RRM are labelled as theirs. Use cases they have not shipped are labelled as possibilities. The Odoo comparison is based on the community source code at github.com/odoo/odoo, branch 19.0, with the 18.0 addon list cross-checked through the GitHub API. File paths are linked.

### Grok Build audit
Not yet run. Waiting on stp to run [BUILD-AUDIT-PROMPT.md](BUILD-AUDIT-PROMPT.md) in Grok Build; its findings will be recorded here.

## What

**Kaspa** is a proof-of-work blockDAG running at 10 blocks per second on mainnet ([kaspa.org/lore](https://kaspa.org/lore)). The Rust node, Rusty Kaspa ([kaspanet/rusty-kaspa](https://github.com/kaspanet/rusty-kaspa)), implemented the Crescendo upgrade to 10 blocks per second ([kaspa.org/lore](https://kaspa.org/lore), [KIP-14](https://github.com/kaspanet/kips/blob/master/kip-0014.md)).

**Odoo** is an ERP system with an open-source edition, developed by Odoo SA (Belgium) ([odoo.com](https://www.odoo.com)). It has an app store ([apps.odoo.com](https://apps.odoo.com)) and pluggable payment providers ([docs](https://www.odoo.com/documentation/18.0/applications/finance/payment_providers.html)).

### Existing implementation: RRM Consultoría module

[RRM Consultoría](https://www.rrmconsultoria.es/) is an ERP consultancy in Sabadell, Spain, that implements Odoo. On 26 Oct 2026 it announced a Kaspa wallet manager for Odoo and a module that certifies Odoo records on Kaspa ([thread](https://x.com/rrm_consultoria/status/2114734310578376709)). According to the thread:

- It certifies purchase receipts, sales delivery notes, manufacturing orders and digital product IDs.
- Each certificate carries a SHA-256 hash, a Kaspa transaction ID and a Merkle inclusion proof against a batch root that is anchored on Kaspa.
- Reported by RRM: a batch of 50 certificates costs about 0.001816 KAS in network fees and typically confirms within 2 minutes ([post](https://x.com/i/status/2114734458679042480)). The batch address they published: [kaspa.stream](https://kaspa.stream/addresses/kaspa:qp6f50fv632mwfk9gjyrjxsvyvrzmxvm03s3vnjs8u5dwhe6sj9x5xlyumxry).
- Stated by RRM: because of MiCA (EU Regulation 2023/1114), they host the wallet and provide it as a service ([post](https://x.com/i/status/2115128191560077807)).
- Scope of the proof, per RRM: it shows the data matches the anchored snapshot; it does not prove the accuracy of the underlying records ([post](https://x.com/i/status/2114735361175757194)).
- No public code repository or Odoo Apps listing was found on 27 Oct 2026. Contact: [rrmconsultoria.es/contacto](https://www.rrmconsultoria.es/contacto).

### Possible applications
- Supply-chain provenance: batch-level links from raw materials to delivery (RRM's example).
- Timestamped audit trails: tamper-evident hashes of records.
- Cross-border invoice settlement: payments on a public network, verifiable by both parties.
- Transfers between subsidiaries.
- Small-value payments.

Only the first two are implemented in RRM's module. The others are not existing Odoo features.

### Not yet available for Odoo
- Tokens (assets, loyalty points): the fungible token standard KCC-20 has `Status: Final` ([kaspanet/kccs, kcc-0020.md](https://github.com/kaspanet/kccs)). Related KCC-23 is still Draft. Wallet and tooling support is incomplete ([master file](https://github.com/STP-KAS/kaspa-master-file)).
- Escrow and conditional payments: covenants / spend rules are live ([docs.kaspa.org](https://docs.kaspa.org)), developer tooling is limited.

## Comparison with Odoo modules

Basis: Odoo community source, branch 19.0. The 18.0 addon list was also checked; the repo's default branch is reported as 20.0. Enterprise-only modules are not in this repository. Status is binary: **Gap** (no community module) or **Handled by Odoo** (Odoo handles it locally). Where Kaspa adds an external proof, that is stated in the "Kaspa adds" column.

| Use case | Existing Odoo (community) | Status | Kaspa adds |
|---|---|---|---|
| Receiving payments | [`payment`](https://github.com/odoo/odoo/tree/19.0/addons/payment) framework plus about 25 `payment_*` providers (e.g. [`payment_stripe`](https://github.com/odoo/odoo/tree/19.0/addons/payment_stripe), [`payment_mollie`](https://github.com/odoo/odoo/tree/19.0/addons/payment_mollie), [`payment_custom`](https://github.com/odoo/odoo/blob/19.0/addons/payment_custom/__manifest__.py) for wire transfers). No crypto-asset provider in 18.0 or 19.0. | Gap | A `payment_*` provider for KAS. None exists publicly. RRM's wallet manager is not a payment provider. |
| Multi-currency invoicing and reconciliation (fiat) | [`res_currency.py`](https://github.com/odoo/odoo/blob/19.0/addons/account/models/res_currency.py), [`account_partial_reconcile.py`](https://github.com/odoo/odoo/blob/19.0/addons/account/models/account_partial_reconcile.py), [`account_reconcile_model.py`](https://github.com/odoo/odoo/blob/19.0/addons/account/models/account_reconcile_model.py) | Handled by Odoo | KAS as a currency, plus matching on-chain payments to invoices. Not implemented. |
| EU e-invoicing | [`account_edi_ubl_cii`](https://github.com/odoo/odoo/tree/19.0/addons/account_edi_ubl_cii), [`account_peppol`](https://github.com/odoo/odoo/tree/19.0/addons/account_peppol) | Handled by Odoo | Optional external timestamp of the invoice hash. Peppol remains the regulated channel. |
| Inalterability of journal entries | `inalterable_hash` and `secure_sequence_number` in [`account_move.py`](https://github.com/odoo/odoo/blob/19.0/addons/account/models/account_move.py) (l.354–355); `restrict_mode_hash_table` in [`account_journal.py`](https://github.com/odoo/odoo/blob/19.0/addons/account/models/account_journal.py); [`account_secure_entries_wizard.py`](https://github.com/odoo/odoo/blob/19.0/addons/account/wizard/account_secure_entries_wizard.py); [`l10n_fr_account`](https://github.com/odoo/odoo/tree/19.0/addons/l10n_fr_account) | Handled by Odoo | Odoo's hash chain is local to the database. Anchoring its root on Kaspa would add an external, independently verifiable timestamp. |
| Lot / serial traceability | [`stock_lot.py`](https://github.com/odoo/odoo/blob/19.0/addons/stock/models/stock_lot.py), [`stock_traceability.py`](https://github.com/odoo/odoo/blob/19.0/addons/stock/report/stock_traceability.py), [`mrp`](https://github.com/odoo/odoo/tree/19.0/addons/mrp) | Handled by Odoo | Third-party-verifiable proof that the records existed in a given state at a given time (RRM's module). |
| Transfers between subsidiaries | No `inter_company*` module in the community repo (18.0/19.0) | Gap | On-chain transfers with public settlement. The accounting link is not implemented. |
| Document signing / DMS | No `sign` or `documents` module in the community repo | Gap | Hash anchoring of signed documents. Not implemented. |

### README claims reviewed
- "Settlement on layer 1 without an intermediary processor": accurate for the transfer itself. In an Odoo setup a hosted wallet provider (as with RRM) can still be an intermediary.
- "Only the first two are implemented in RRM's module": consistent with the thread. No code is public, so this is unverified beyond RRM's posts and screenshots.
- No other corrections found.

## Relevant properties

| Claim | Source |
|---|---|
| 10 blocks per second since Crescendo (it was 1 before) | [KIP-14 (Active)](https://github.com/kaspanet/kips/blob/master/kip-0014.md), [kaspa.org/lore](https://kaspa.org/lore) |
| Fee data point: about 0.001816 KAS for a batch of 50 certificates (RRM's figure, one data point) | [RRM post](https://x.com/i/status/2114734458679042480) |
| Proof-of-work consensus; no premine | [kaspa.org](https://kaspa.org), [master file](https://github.com/STP-KAS/kaspa-master-file) |
| Transactions are publicly verifiable via explorers | [explorer.kaspa.org](https://explorer.kaspa.org), [kaspa.stream](https://kaspa.stream) |
| Settlement is permissionless; a transaction moves value from sender to receiver directly on layer 1. The protocol checks signatures and scripts, not whether the key holder is a person, a business or a software agent. | [kaspa.org/lore](https://kaspa.org/lore) ("permissionless settlement"), [docs.kaspa.org](https://docs.kaspa.org) |
| Settlement on layer 1 without an intermediary processor | [kaspa.org](https://kaspa.org) |

## Outlook (not available now)

- **Next hard fork: DAGKnight.** kaspa.org/lore describes the "DAGKnight hardfork" as the consensus upgrade that follows Toccata, with block times targeted at 40–25 ms, and lists a separate later "2027 hardfork" for 100 blocks per second ([kaspa.org/lore](https://kaspa.org/lore)). The master file records DAGKnight as not shipped, with development on the rusty-kaspa `dagknight` branch ([master file](https://github.com/STP-KAS/kaspa-master-file), [rusty-kaspa](https://github.com/kaspanet/rusty-kaspa)). No mainnet date is confirmed. Toccata (L1 covenants) is already live, at DAA score 474165565 per the master file.
- **Stablecoins on Kaspa.** KCC-20 (fungible token covenant) is `Status: Final` ([kaspanet/kccs](https://github.com/kaspanet/kccs)), which gives a standard that dollar-denominated tokens could use. On 27 Oct 2026 no primary source (KIP, KCC, kaspanet repo or issuer announcement) was found that confirms a "Kaspa dollar", a Tether (USDT) issuance on Kaspa L1, or several stablecoins. The master file lists a spendable L1 stablecoin as not shipped. It also notes that bridged USDT keeps the issuer's freeze key. This item is not confirmed.
  - *Side note (27 Oct 2026):* [Kaspa Dollar](https://x.com/KaspaDollar) ([kaspadollar.com](https://kaspadollar.com)). Draft / not live; noted for future relevance. The project describes itself as "an initiative led by core developer @asaefstroem" (Alexander Säfström; GitHub [saefstroem](https://github.com/saefstroem), X [@asaefstroem](https://x.com/asaefstroem)) to build a decentralized, overcollateralized stablecoin on Kaspa ([announcement, 23 Oct 2026](https://x.com/KaspaDollar/status/2113601328975098237)). Michael Sutton publicly offered technical support ([24 Oct 2026](https://x.com/michaelsuttonil/status/2114040191719444646)). Status on 27 Oct 2026: a donation fundraiser with a target of 5,000,000 KAS, refundable if not reached by 1 Jan 2027. No whitepaper, code or contract has been published yet.

## Sources and references
- This repository: https://github.com/STP-KAS/kaspa-odoo
- Kaspa master file: https://github.com/STP-KAS/kaspa-master-file
- kaspanet GitHub org: https://github.com/kaspanet. "Rusty Kaspa" is the Rust node, https://github.com/kaspanet/rusty-kaspa
- KIPs (Kaspa Improvement Proposals): https://github.com/kaspanet/kips. KCCs (token and contract conventions): https://github.com/kaspanet/kccs
- Core developers: Michael Sutton ( [GitHub](https://github.com/michaelsutton), [X](https://x.com/michaelsuttonil)). For a fuller list, see the "Who" section of the master file and the kaspanet contributor graphs. "Core" is not a formal title.
- Research forum: https://research.kas.pa (Crescendo thread: https://research.kas.pa/t/crescendo-hardfork-discussion-thread/279)
- Kaspa site: https://kaspa.org · lore: https://kaspa.org/lore · docs: https://docs.kaspa.org · wiki: https://wiki.kaspa.org
- Explorers: https://explorer.kaspa.org · https://kaspa.stream
- Odoo: https://www.odoo.com · Source code (official repo): https://github.com/odoo/odoo · Apps: https://apps.odoo.com · payment providers: https://www.odoo.com/documentation/18.0/applications/finance/payment_providers.html
- Odoo source paths used: https://github.com/odoo/odoo/tree/19.0/addons/payment · https://github.com/odoo/odoo/tree/19.0/addons/account · https://github.com/odoo/odoo/tree/19.0/addons/stock · https://github.com/odoo/odoo/tree/19.0/addons/mrp · https://github.com/odoo/odoo/tree/19.0/addons/account_peppol · https://github.com/odoo/odoo/tree/19.0/addons/l10n_fr_account
- RRM Consultoría: https://www.rrmconsultoria.es/ · announcement thread: https://x.com/rrm_consultoria/status/2114734310578376709

This document contains no price information or financial advice.
