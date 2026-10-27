# Kaspa and Odoo

Independent write-up, 27 Oct 2026. Not affiliated with Odoo SA, RRM Consultoría or the Kaspa developers. Each statement links to its source. Review notes: [Grok bots audit](#grok-bots-audit).

## Grok bots audit

### TN10 ops2 view
This write-up was compiled on 27 Oct 2026 from primary sources: RRM's X thread and site, the KIPs/KCCs repos, kaspa.org, Odoo's docs, and the STP-KAS master file. Every URL was checked with curl (all returned 200, except explorer.kaspa.org, which returns 403 to scripted requests). Claims that come only from RRM are labelled as theirs. Use cases they have not shipped are labelled as possibilities.

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

## Relevant properties

| Claim | Source |
|---|---|
| 10 blocks per second since Crescendo (it was 1 before) | [KIP-14 (Active)](https://github.com/kaspanet/kips/blob/master/kip-0014.md), [kaspa.org/lore](https://kaspa.org/lore) |
| Fee data point: about 0.001816 KAS for a batch of 50 certificates (RRM's figure, one data point) | [RRM post](https://x.com/i/status/2114734458679042480) |
| Proof-of-work consensus; no premine | [kaspa.org](https://kaspa.org), [master file](https://github.com/STP-KAS/kaspa-master-file) |
| Transactions are publicly verifiable via explorers | [explorer.kaspa.org](https://explorer.kaspa.org), [kaspa.stream](https://kaspa.stream) |
| Settlement on layer 1 without an intermediary processor | [kaspa.org](https://kaspa.org) |

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
- RRM Consultoría: https://www.rrmconsultoria.es/ · announcement thread: https://x.com/rrm_consultoria/status/2114734310578376709

This document contains no price information or financial advice.
