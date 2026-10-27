# Kaspa and Odoo

Independent write-up, 27 Oct 2026. Not affiliated with Odoo SA or the Kaspa developers. Each statement links to its source. Review notes: [Audits](#audits).

> **Disclaimer.** The author is not a Kaspa contributor and does not take part in Kaspa development in any way. They are an interested individual who tests on Kaspa's testnet. Kaspa is open source ([official Kaspa GitHub organization](https://github.com/kaspanet)) and decentralized: anyone can build on it.

## Summary

Kaspa is a proof-of-work blockDAG that produces 10 blocks per second on mainnet. kaspa.org/lore names permissionless settlement among the properties it pairs with proof of work. Odoo's community code (branches 18.0 and 19.0) contains no Kaspa integration and no crypto-asset payment provider. Against that code, three rows are gaps: a KAS payment provider, transfers between subsidiaries, and document signing. Sign and Documents are Enterprise-only on Odoo's editions page. The inter-company user doc describes the feature and does not name an edition. Where Odoo already handles multi-currency invoicing, journal hashes, lot traceability, or e-invoicing, Kaspa would add an external timestamp. An 8-decimal KAS unit does not fit Odoo's currency rounding field.

Kaspa core: [github.com/kaspanet](https://github.com/kaspanet). Reliable sources: [kaspa.org](https://kaspa.org), [docs.kaspa.org](https://docs.kaspa.org), [research.kas.pa](https://research.kas.pa), [qa.kas.pa](https://qa.kas.pa/), [Kaspa Discord](https://discord.gg/kaspa), [t.me/kasparnd](https://t.me/kasparnd), [kaspa.news](https://kaspa.news), [Kaspa Explained](https://kaspaexplained.com/status).

## Audits

### Audit by TN10 ops2
This write-up was compiled on 27 Oct 2026 from primary sources: the KIPs/KCCs repos, kaspa.org, Odoo's docs, and the STP-KAS master file. Every URL was checked with curl (all returned 200, except explorer.kaspa.org, which returns 403 to scripted requests). Use cases that are not implemented are labelled as possibilities. The Odoo comparison is based on the community source code at github.com/odoo/odoo, branch 19.0, with the 18.0 addon list cross-checked through the GitHub API. File paths are linked.

### Audit by Grok Build

**Opinion:** The community comparison is right about the files it names. Payments in KAS are a gap. Invoicing, journal hashes, lot traceability, and e-invoicing are handled in the community tree. The payment-module count was high, and an 8-decimal unit does not fit `res.currency`'s rounding field. The glossary's sompi line matches the node constant. DAGKnight is still KIP-2, status Proposed. Kaspa Dollar is an announcement and a roadmap on their site. It is not a KCC, and it is not a shipped token.

**Summary and glossary check:** The summary matched the body's gaps, and it added two clauses the cited pages do not state: "without an intermediary," and "mature tooling." Those are removed. The glossary lines for blockDAG, 10 BPS, sompi, Toccata covenants, and KCC-20's Final status match lore, `SOMPI_PER_KASPA`, KIP-17, and kcc-0020.md. KCC-0 limits what Final means, so that limit is now in the glossary. The UTXO line matches KIP-17's use of UTXOs. The anchoring line is a general definition and is not tied to a deployment in this file. Peppol matches the `account_peppol` path. The MiCA line names Regulation (EU) 2023/1114. EUR-Lex returned HTTP 202 and an empty body to this client, so the legal text was not re-read. The term is not used in the body.

**Comparison:**

| Use case | Status | What this pass found |
|---|---|---|
| Receiving payments | Gap | 23 `payment_*` modules on 19.0, 17 on 18.0, plus the `payment` framework. No Kaspa provider in that tree. |
| Multi-currency invoicing and reconciliation (fiat) | Handled by Odoo | Fiat rounding and reconciliation are in the community tree. An 8-decimal unit does not fit `rounding`. |
| EU e-invoicing | Handled by Odoo | `account_edi_ubl_cii` and `account_peppol` are in the 19.0 tree. |
| Inalterability of journal entries | Handled by Odoo | `secure_sequence_number` and `inalterable_hash` are lines 354–355. The hash stays in the database. |
| Lot / serial traceability | Handled by Odoo | `stock_lot.py`, the traceability report, and `mrp` are in the 19.0 tree. |
| Transfers between subsidiaries | Gap | No `inter_company` addon on 18.0, 19.0, or 20.0. The user doc describes the feature and does not name an edition. |
| Document signing / DMS | Gap | No `sign` or `documents` addon on 18.0, 19.0, or 20.0. The editions page marks both Enterprise-only. |

The binary labels stand. This pass disagrees with the payment count, the file that defines `rounding`, the missing edition note on Sign and Documents, and the missing statement that the inter-company doc does not name an edition.

**Findings:**
- Odoo 19.0 has 23 modules named `payment_*`, and 18.0 has 17, plus the `payment` framework. None of the names is a crypto-asset provider. Counted from the addon directories on [github.com/odoo/odoo](https://github.com/odoo/odoo). The default branch is 20.0 (pushed 27 Oct 2026).
- `account_move.py` lines 354–355 are `secure_sequence_number` and `inalterable_hash`. `restrict_mode_hash_table` is defined on the journal at line 145. [account_move.py](https://github.com/odoo/odoo/blob/19.0/addons/account/models/account_move.py), [account_journal.py](https://github.com/odoo/odoo/blob/19.0/addons/account/models/account_journal.py).
- `res.currency.rounding` is stored with `digits=(12, 6)`. `decimal_places` is computed from that factor. [res_currency.py](https://github.com/odoo/odoo/blob/19.0/odoo/addons/base/models/res_currency.py) lines 37–40 and 162–168. The account module inherits the model and adds a rounding warning. One sompi is 10^-8 KAS ([`SOMPI_PER_KASPA`](https://github.com/kaspanet/rusty-kaspa/blob/master/consensus/core/src/constants.rs) is `100_000_000`). [KIP-14](https://github.com/kaspanet/kips/blob/master/kip-0014.md) says the Crescendo change raises blocks per second from 1 to 10.
- The [editions comparison](https://www.odoo.com/page/editions) marks Sign and Documents absent from Community and present in Enterprise. No addon named `sign`, `documents`, or `inter_company` is in the community repo on 18.0, 19.0, or 20.0. The [multi-company doc](https://www.odoo.com/documentation/19.0/applications/general/companies/multi_company.html) describes inter-company transactions as a Settings feature and does not call the feature Enterprise.
- The Apps store lists third-party crypto providers. Confirmed pages: [BTCPay Server for 17.0](https://apps.odoo.com/apps/modules/17.0/payment_btcpayserver) (the 18.0 and 19.0 URLs tried returned 404) and [OpenNode for 19.0](https://apps.odoo.com/apps/modules/19.0/payment_opennode). A search for `kaspa` did not list a Kaspa module. [Kaspi.kz](https://apps.odoo.com/apps/modules/19.0/mp_kaspi_kz) is a Kazakhstan marketplace.
- [kaspa.org](https://kaspa.org) says the network runs at 10 blocks per second and says "No premine." [kaspa.org/lore](https://kaspa.org/lore) says mainnet launched in November 2021 with no premine, no insider allocation, and no pre-sales, names "permissionless settlement," describes Toccata as activated on 30 Jun 2026 at DAA score 474165565, describes DAGKnight as the next hard fork with target block times of 40–25 ms, and lists a separate later "2027 hardfork" for 100 blocks per second. Lore gives no mainnet date for DAGKnight.
- [KIP-2](https://github.com/kaspanet/kips/blob/master/kip-0002.md) is Proposed. Its title is the DAGKNIGHT upgrade. The [rusty-kaspa `dagknight` branch](https://github.com/kaspanet/rusty-kaspa/tree/dagknight) tip is `ad45e241` (8 Sep 2026). Compared with `master` it is ahead 129, behind 14, and diverged. [Topic 295](https://research.kas.pa/t/a-proposal-towards-elastic-throughput/295) (hashdag, 5 Feb 2025) mentions DAGKNIGHT inside a throughput proposal. It is not an activation notice. [rusty-kaspa #1082](https://github.com/kaspanet/rusty-kaspa/pull/1082), merged 19 Aug 2026, states the Toccata activation above. [KIP-17](https://github.com/kaspanet/kips/blob/master/kip-0017.md) is Active.
- [kcc-0020.md](https://github.com/kaspanet/kccs/blob/main/kcc-0020.md) says `Status: Final` (commit [`9dec4a04`](https://github.com/kaspanet/kccs/commit/9dec4a0421326a99c70d07f0aa11d11bc894ab9b), 21 Oct 2026). [kcc-0023.md](https://github.com/kaspanet/kccs/blob/main/kcc-0023.md) says Draft. [KCC-0](https://github.com/kaspanet/kccs/blob/main/kcc-0000.md) says a Final KCC is an ecosystem convergence point and does not make the convention protocol ground truth. The kccs tree has no stablecoin document. The [master file](https://github.com/STP-KAS/kaspa-master-file) Not-live cell says there is no spendable L1 stable. A search of that README found no sentence about bridged USDT and a freeze key.
- [kaspaexplained.com/status](https://kaspaexplained.com/status) says "Sources checked October 6, 2026" and still calls KCC-20 Draft, and KCC-1 and KCC-2 Last Call. The kccs files say KCC-20 is Final.
- [KIP-16](https://github.com/kaspanet/kips/blob/master/kip-0016.md) is Active. Its authors line is Alexander Safstrom (@saefstroem). [@KaspaDollar](https://x.com/KaspaDollar/status/2113601328975098237) on 23 Oct 2026 described an initiative led by @asaefstroem, an overcollateralized design, a fundraiser target, and a 1 Jan 2027 return condition, and linked [kaspadollar.com](https://kaspadollar.com). The site and its [docs page](https://kaspadollar.com/docs) returned HTTP 200. The docs page is titled "Roadmap 2027" and states their own later dates, including a testnet in May and a mainnet in September. @asaefstroem wrote "Covenants will issue it" ([24 Oct 2026](https://x.com/asaefstroem/status/2113970020598632570)). Michael Sutton offered technical help ([24 Oct 2026](https://x.com/michaelsuttonil/status/2114040191719444646)). A GitHub search for a repository named kaspadollar returned none. A Tether issuance on Kaspa, and several stablecoins, are not confirmed.
- [docs.kaspa.org](https://docs.kaspa.org) links a [Toccata guide](https://docs.kaspa.org/toccata). `https://docs.kaspa.org/docs/toccata/based-apps` returned 404. [explorer.kaspa.org](https://explorer.kaspa.org) returned 403 to this client. [kaspa.stream](https://kaspa.stream), [wiki.kaspa.org](https://wiki.kaspa.org), [research.kas.pa](https://research.kas.pa), [qa.kas.pa](https://qa.kas.pa/), [discord.gg/kaspa](https://discord.gg/kaspa), [t.me/kasparnd](https://t.me/kasparnd), and [kaspa.news](https://kaspa.news) returned HTTP 200. This pass did not read the Telegram channel or match posts to kaspa.news.

**Corrections needed:**
- "about 25 `payment_*` providers." The 19.0 directory has 23 names beginning with `payment_`. The 18.0 directory has 17.
- Multi-currency pointed at `addons/account/models/res_currency.py`. That file inherits `res.currency` and adds a warning. The `rounding` field is defined on `odoo/addons/base/models/res_currency.py` with `digits=(12, 6)`.
- "None of these is an existing Odoo feature." Odoo documents payments and inter-company transactions. The Kaspa uses in that list are not Odoo modules.
- "without an intermediary" in the summary, and the property-table sentence that the protocol checks signatures and scripts rather than whether the key holder is a person, a business, or a software agent. Lore uses the phrase "permissionless settlement." The homepage and the docs homepage do not contain those longer sentences. docs.kaspa.org was cited for the property row and does not state it.
- KCC-20 was linked to the kccs repository root. The status line is in kcc-0020.md. "Wallet and tooling support is incomplete" and "developer tooling is limited" had no source in the master file or in KIP-17.
- "bridged USDT keeps the issuer's freeze key." That sentence is not in the current master-file README.
- "No whitepaper, code or contract has been published yet." The Kaspa Dollar site publishes a roadmap. This pass found no repository named kaspadollar and did not find a specification.
- kaspaexplained.com/status is listed as a page of what is live. Its own date is 6 Oct 2026, and its KCC-20 line is behind kcc-0020.md.
- The Telegram line said the channel is mostly read-only and that recaps are published on kaspa.news. This pass confirmed the URLs and did not confirm those two sentences.

**Fixes made:** Corrected the `payment_*` counts. Pointed multi-currency at the base `res.currency` rounding field and stated the 8-decimal gap. Cited the editions page for Sign and Documents, and left inter-company's edition unmarked. Rewrote the summary so it matches those rows and drops the unsourced clauses. Linked KCC-20, KCC-23, and KCC-0, and removed the unsourced tooling clauses. Replaced the covenant citation with KIP-17, lore, and rusty-kaspa #1082. Narrowed the settlement row to lore's phrase. Pointed the no-premine line at kaspa.org and kaspa.org/lore. Removed the bridged-USDT freeze sentence. Stated the Kaspa Dollar roadmap as theirs. Noted the 6 Oct kaspaexplained page, the explorer 403, and the unread Telegram claim. Added the Toccata date and the KCC-0 limit to the glossary.

**Suggestions / additions:** The provider hooks a KAS module would implement are under the comparison. Still open: inter-company's edition is not stated on the page that documents it; EUR-Lex did not return the regulation text to this client; the Kaspa Dollar roadmap dates are the site's dates. This pass did not copy the live fundraiser progress.

**Sources checked:** 27 Oct 2026. Odoo: github.com/odoo/odoo branches 18.0, 19.0, and 20.0, the file paths in the table, [payment docs 18.0](https://www.odoo.com/documentation/18.0/applications/finance/payment_providers.html) and [19.0](https://www.odoo.com/documentation/19.0/applications/finance/payment_providers.html), the [editions page](https://www.odoo.com/page/editions), the [about page](https://www.odoo.com/page/about-us), the [multi-company doc](https://www.odoo.com/documentation/19.0/applications/general/companies/multi_company.html), and [apps.odoo.com](https://apps.odoo.com). Kaspa: [kaspa.org](https://kaspa.org), [lore](https://kaspa.org/lore), [docs.kaspa.org](https://docs.kaspa.org), [wiki.kaspa.org](https://wiki.kaspa.org), [research.kas.pa](https://research.kas.pa), topic 279, topic 295, rusty-kaspa `master` and `dagknight`, `constants.rs`, pull 1082, KIP-2, KIP-14, KIP-16, KIP-17, KCC-0, KCC-20, KCC-23, and the [master file](https://github.com/STP-KAS/kaspa-master-file). Also [kaspaexplained.com/status](https://kaspaexplained.com/status), [qa.kas.pa](https://qa.kas.pa/), [discord.gg/kaspa](https://discord.gg/kaspa), [t.me/kasparnd](https://t.me/kasparnd), [kaspa.news](https://kaspa.news), [kaspa.stream](https://kaspa.stream), and explorer.kaspa.org (HTTP 403). Kaspa Dollar: the 23 Oct post, the site and [/docs](https://kaspadollar.com/docs), [@asaefstroem](https://x.com/asaefstroem/status/2113970020598632570), [github.com/saefstroem](https://github.com/saefstroem), and [Sutton's reply](https://x.com/michaelsuttonil/status/2114040191719444646). EUR-Lex for 32023R1114 returned HTTP 202 and an empty body.

**Date:** 27 Oct 2026. **Commit:** `9c944c009e9bcd01010bda879e6753c5ae0a1d6b`. The prompt is [BUILD-AUDIT-PROMPT.md](BUILD-AUDIT-PROMPT.md).

## What

**Kaspa** is a proof-of-work blockDAG running at 10 blocks per second on mainnet ([kaspa.org/lore](https://kaspa.org/lore)). The Rust node, Rusty Kaspa ([kaspanet/rusty-kaspa](https://github.com/kaspanet/rusty-kaspa)), implemented the Crescendo upgrade to 10 blocks per second ([kaspa.org/lore](https://kaspa.org/lore), [KIP-14](https://github.com/kaspanet/kips/blob/master/kip-0014.md)).

**Odoo** is an ERP system with an open-source edition, developed by Odoo SA (Belgium) ([odoo.com](https://www.odoo.com)). It has an app store ([apps.odoo.com](https://apps.odoo.com)) and pluggable payment providers ([docs](https://www.odoo.com/documentation/18.0/applications/finance/payment_providers.html)).

### Possible applications
- Supply-chain provenance: batch-level links from raw materials to delivery, anchored on Kaspa.
- Timestamped audit trails: tamper-evident hashes of records.
- Cross-border invoice settlement: payments on a public network, verifiable by both parties.
- Transfers between subsidiaries.
- Small-value payments.

None of these is a Kaspa feature in Odoo. Odoo documents payments and inter-company transactions. See the [comparison](#comparison-with-odoo-modules) for how each line relates to existing modules.

### Not yet available for Odoo
- Tokens (assets, loyalty points): the fungible token standard KCC-20 has `Status: Final` ([kcc-0020.md](https://github.com/kaspanet/kccs/blob/main/kcc-0020.md), commit [`9dec4a04`](https://github.com/kaspanet/kccs/commit/9dec4a0421326a99c70d07f0aa11d11bc894ab9b), 21 Oct 2026). Related [KCC-23](https://github.com/kaspanet/kccs/blob/main/kcc-0023.md) is Draft. [KCC-0](https://github.com/kaspanet/kccs/blob/main/kcc-0000.md) says a Final KCC is an ecosystem convergence point and does not make the convention protocol ground truth.
- Escrow and conditional payments: [KIP-17](https://github.com/kaspanet/kips/blob/master/kip-0017.md) is Active and specifies covenant support. [kaspa.org/lore](https://kaspa.org/lore) says Toccata activated on 30 Jun 2026 at DAA score 474165565. [rusty-kaspa #1082](https://github.com/kaspanet/rusty-kaspa/pull/1082), merged 19 Aug 2026, states the same activation. [docs.kaspa.org](https://docs.kaspa.org) links a [Toccata guide](https://docs.kaspa.org/toccata).

## Comparison with Odoo modules

Basis: Odoo community source, branch 19.0. The 18.0 and 20.0 addon lists were also checked. The repo's default branch is 20.0. Status is binary: **Gap** (no community module) or **Handled by Odoo** (Odoo handles it locally). Where Kaspa adds an external proof, that is stated in the "Kaspa adds" column. Sign and Documents are marked Enterprise-only on the [editions page](https://www.odoo.com/page/editions). The inter-company doc does not name an edition.

| Use case | Existing Odoo (community) | Status | Kaspa adds |
|---|---|---|---|
| Receiving payments | [`payment`](https://github.com/odoo/odoo/tree/19.0/addons/payment) framework plus 23 `payment_*` modules on 19.0 and 17 on 18.0 (e.g. [`payment_stripe`](https://github.com/odoo/odoo/tree/19.0/addons/payment_stripe), [`payment_mollie`](https://github.com/odoo/odoo/tree/19.0/addons/payment_mollie), [`payment_custom`](https://github.com/odoo/odoo/blob/19.0/addons/payment_custom/__manifest__.py) for wire transfers). No crypto-asset provider in those community directories. | Gap | A `payment_*` provider for KAS. None is in the community tree, and an Apps-store search for `kaspa` did not list one. |
| Multi-currency invoicing and reconciliation (fiat) | [`res_currency.py`](https://github.com/odoo/odoo/blob/19.0/odoo/addons/base/models/res_currency.py) (`rounding`, `digits=(12, 6)`, lines 37–40; `decimal_places` at lines 162–168), [`account_partial_reconcile.py`](https://github.com/odoo/odoo/blob/19.0/addons/account/models/account_partial_reconcile.py), [`account_reconcile_model.py`](https://github.com/odoo/odoo/blob/19.0/addons/account/models/account_reconcile_model.py) | Handled by Odoo | KAS as an 8-decimal unit does not fit that rounding field. One sompi is 10^-8 KAS. Matching on-chain payments to invoices is not implemented. |
| EU e-invoicing | [`account_edi_ubl_cii`](https://github.com/odoo/odoo/tree/19.0/addons/account_edi_ubl_cii), [`account_peppol`](https://github.com/odoo/odoo/tree/19.0/addons/account_peppol) | Handled by Odoo | Optional external timestamp of the invoice hash. Peppol remains the regulated channel. |
| Inalterability of journal entries | `inalterable_hash` and `secure_sequence_number` in [`account_move.py`](https://github.com/odoo/odoo/blob/19.0/addons/account/models/account_move.py) (l.354–355); `restrict_mode_hash_table` in [`account_journal.py`](https://github.com/odoo/odoo/blob/19.0/addons/account/models/account_journal.py); [`account_secure_entries_wizard.py`](https://github.com/odoo/odoo/blob/19.0/addons/account/wizard/account_secure_entries_wizard.py); [`l10n_fr_account`](https://github.com/odoo/odoo/tree/19.0/addons/l10n_fr_account) | Handled by Odoo | Odoo's hash chain is local to the database. Anchoring its root on Kaspa would add an external, independently verifiable timestamp. |
| Lot / serial traceability | [`stock_lot.py`](https://github.com/odoo/odoo/blob/19.0/addons/stock/models/stock_lot.py), [`stock_traceability.py`](https://github.com/odoo/odoo/blob/19.0/addons/stock/report/stock_traceability.py), [`mrp`](https://github.com/odoo/odoo/tree/19.0/addons/mrp) | Handled by Odoo | Third-party-verifiable proof that the records existed in a given state at a given time |
| Transfers between subsidiaries | No addon named `inter_company` in the community repo (18.0, 19.0, or 20.0). The [multi-company doc](https://www.odoo.com/documentation/19.0/applications/general/companies/multi_company.html) describes inter-company transactions and does not name an edition. | Gap | On-chain transfers with public settlement. The accounting link is not implemented. |
| Document signing / DMS | No addon named `sign` or `documents` in the community repo (18.0, 19.0, or 20.0). The [editions page](https://www.odoo.com/page/editions) marks both absent from Community and present in Enterprise. | Gap | Hash anchoring of signed documents. Not implemented. |

### Payment provider interface
A community provider subclasses `payment.provider` and `payment.transaction` in the `payment` module. [`payment_custom`](https://github.com/odoo/odoo/blob/19.0/addons/payment_custom/__manifest__.py) describes itself as a provider for custom flows such as wire transfers. Its transaction model implements `_get_specific_rendering_values`, `_get_communication`, `_extract_amount_data`, and `_apply_updates`. [`payment_demo`](https://github.com/odoo/odoo/blob/19.0/addons/payment_demo/models/payment_transaction.py) also implements `_send_payment_request`, `_send_capture_request`, `_send_void_request`, and `_send_refund_request`. The base provider implements `_get_supported_currencies`. A watch-only KAS flow, an address plus a later confirmation, is closer to `payment_custom` than to the capture, void, and refund methods.

## Relevant properties

| Claim | Source |
|---|---|
| 10 blocks per second since Crescendo (it was 1 before) | [KIP-14 (Active)](https://github.com/kaspanet/kips/blob/master/kip-0014.md), [kaspa.org/lore](https://kaspa.org/lore) |
| Proof-of-work consensus; no premine | [kaspa.org](https://kaspa.org) ("No premine. No hidden allocation. Fair launch."), [kaspa.org/lore](https://kaspa.org/lore) (November 2021, no premine, no insider allocation, no pre-sales) |
| Transactions are publicly verifiable | [kaspa.stream](https://kaspa.stream) returned HTTP 200. [explorer.kaspa.org](https://explorer.kaspa.org) returned 403 to this client. |
| Lore names permissionless settlement together with censorship resistance and competitive mining | [kaspa.org/lore](https://kaspa.org/lore) |

## Side note: Kaspa Dollar

Kaspa is already in use and active as a network, but KAS itself is a volatile asset. For businesses that invoice and keep their books in fiat, that volatility is a practical barrier. This is why a dollar stablecoin on Kaspa is being explored. If it materializes, it could be an important step for Kaspa: it would make stable-value settlement possible on Kaspa, and it would leave room for stablecoin issuers, Tether-like or otherwise, to operate on the network. None of this exists or is confirmed today.

[Kaspa Dollar](https://x.com/KaspaDollar) ([kaspadollar.com](https://kaspadollar.com)) is one such initiative. It is a draft, not a live token. The project describes itself as led by core developer Alexander Säfström (GitHub [saefstroem](https://github.com/saefstroem), X [@asaefstroem](https://x.com/asaefstroem)) and aims at a decentralized, overcollateralized stablecoin ([announcement, 23 Oct 2026](https://x.com/KaspaDollar/status/2113601328975098237)). Säfström is the author of [KIP-16](https://github.com/kaspanet/kips/blob/master/kip-0016.md) in the kaspanet repo. Michael Sutton publicly offered technical support ([24 Oct 2026](https://x.com/michaelsuttonil/status/2114040191719444646)). The announcement states a fundraiser with a target of 5,000,000 KAS, refundable if the target is not reached by 1 Jan 2027. As of 27 Oct 2026, no code repository or specification had been found.

## Quick reference: reliable sources

Links checked on 27 Oct 2026. Each one is followed by what you will find there.

**Official Kaspa**
- [kaspa.org](https://kaspa.org): project site.
- [kaspa.org/lore](https://kaspa.org/lore): history and upgrade status (Crescendo, Toccata, DAGKnight).
- [github.com/kaspanet](https://github.com/kaspanet): official GitHub organisation.
- [kaspanet/rusty-kaspa](https://github.com/kaspanet/rusty-kaspa): the Rust node ("Rusty Kaspa").
- [kaspanet/kips](https://github.com/kaspanet/kips): Kaspa Improvement Proposals (protocol changes, e.g. KIP-14 Crescendo).

**Docs and research**
- [docs.kaspa.org](https://docs.kaspa.org): official documentation; start here.
- [wiki.kaspa.org](https://wiki.kaspa.org): community wiki.
- [research.kas.pa](https://research.kas.pa): protocol research forum ([Crescendo thread](https://research.kas.pa/t/crescendo-hardfork-discussion-thread/279)).
- [kaspaexplained.com/status](https://kaspaexplained.com/status): community status page ([source](https://github.com/parker2017code/kaspa-explained)). The page says "Sources checked October 6, 2026" and still calls KCC-20 Draft. [kcc-0020.md](https://github.com/kaspanet/kccs/blob/main/kcc-0020.md) says Final as of 21 Oct 2026. Read it alongside the KCC files.
- [Kaspa master file](https://github.com/STP-KAS/kaspa-master-file): sourced community fact base (STP-KAS), including the "Who" list of contributors. "Core" is not a formal title. Example: Michael Sutton ([GitHub](https://github.com/michaelsutton), [X](https://x.com/michaelsuttonil)).

**Community help**
- [Kaspa Discord](https://discord.gg/kaspa): the invite URL returned HTTP 200 on 27 Oct 2026. This pass did not read the channel list.
- [qa.kas.pa](https://qa.kas.pa/): written Q&A. The page title is Kaspa Q&A.
- [t.me/kasparnd](https://t.me/kasparnd) and [kaspa.news](https://kaspa.news): both URLs returned HTTP 200. This pass did not read the channel or match posts to the news site.

**Explorers**
- [explorer.kaspa.org](https://explorer.kaspa.org): returned HTTP 403 to this client on 27 Oct 2026. [kaspa.stream](https://kaspa.stream): returned HTTP 200.

**Wallets and tools**
- None are listed here yet. Ask in the Discord wallets channel, or see docs.kaspa.org.

**Tokens and stablecoins**
- [kaspanet/kccs](https://github.com/kaspanet/kccs): token and contract conventions. [KCC-20](https://github.com/kaspanet/kccs/blob/main/kcc-0020.md) is Final. [KCC-23](https://github.com/kaspanet/kccs/blob/main/kcc-0023.md) is Draft. [KCC-0](https://github.com/kaspanet/kccs/blob/main/kcc-0000.md) says Final is not protocol ground truth.
- [Kaspa Dollar](https://x.com/KaspaDollar) · [kaspadollar.com](https://kaspadollar.com): stablecoin initiative, in fundraising, not live.

**Odoo**
- [odoo.com](https://www.odoo.com) · [apps.odoo.com](https://apps.odoo.com) · [payment providers docs](https://www.odoo.com/documentation/18.0/applications/finance/payment_providers.html).
- [github.com/odoo/odoo](https://github.com/odoo/odoo): official source. Paths used in this report: [payment](https://github.com/odoo/odoo/tree/19.0/addons/payment) · [account](https://github.com/odoo/odoo/tree/19.0/addons/account) · [stock](https://github.com/odoo/odoo/tree/19.0/addons/stock) · [mrp](https://github.com/odoo/odoo/tree/19.0/addons/mrp) · [account_peppol](https://github.com/odoo/odoo/tree/19.0/addons/account_peppol) · [l10n_fr_account](https://github.com/odoo/odoo/tree/19.0/addons/l10n_fr_account).

**This report**
- [STP-KAS/kaspa-odoo](https://github.com/STP-KAS/kaspa-odoo).

This document contains no price information or financial advice.
