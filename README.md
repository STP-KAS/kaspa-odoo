# Kaspa × Odoo

*Independent community write-up, dated 27 Oct 2026. Not affiliated with Odoo SA, RRM Consultoría or the Kaspa core developers. Facts only. Each claim links to its source. See [Grok bots audit](#grok-bots-audit).*

## Grok bots audit

### TN10 ops2 view
This write-up was compiled on 27 Oct 2026 from primary sources: RRM's X thread and site, the KIPs/KCCs repos, kaspa.org, Odoo's docs, and the STP-KAS master file. Every URL was checked with curl (all returned 200, except explorer.kaspa.org, which returns 403 to scripted requests). Claims that come only from RRM are labelled as theirs. Use cases they have not shipped are labelled as possibilities.

### Grok Build audit
*Pending. Grok Build writes its findings here (see [BUILD-AUDIT-PROMPT.md](BUILD-AUDIT-PROMPT.md)).*

## What

**Kaspa** is a proof-of-work blockDAG running at 10 blocks per second on mainnet ([kaspa.org/lore](https://kaspa.org/lore)). The Rust node, Rusty Kaspa ([kaspanet/rusty-kaspa](https://github.com/kaspanet/rusty-kaspa)), is what made the Crescendo upgrade to 10 BPS possible ([kaspa.org/lore](https://kaspa.org/lore), [KIP-14](https://github.com/kaspanet/kips/blob/master/kip-0014.md)).

**Odoo** is an open-source ERP made by a Belgian company ([odoo.com](https://www.odoo.com)). It has an app store ([apps.odoo.com](https://apps.odoo.com)) and pluggable payment providers ([docs](https://www.odoo.com/documentation/18.0/applications/finance/payment_providers.html)).

### What exists today: RRM Consultoría's Kaspa module for Odoo

[RRM Consultoría](https://www.rrmconsultoria.es/) is an Odoo partner in Sabadell, Spain. On 26 Oct 2026 it announced a **Kaspa Wallet Manager inside Odoo ERP**, together with **cryptographic certification of Odoo records** ([thread](https://x.com/rrm_consultoria/status/2114734310578376709)). According to the thread:

- It certifies purchase receipts, sales delivery notes, manufacturing orders and digital product IDs.
- Each certificate carries a SHA-256 hash, a Kaspa transaction ID and a Merkle inclusion proof against a batch root that is anchored on Kaspa.
- **Their figures:** a batch of 50 certificates costs about 0.001816 KAS in network fees and typically confirms within 2 minutes ([post](https://x.com/i/status/2114734458679042480)). The batch address they published: [kaspa.stream](https://kaspa.stream/addresses/kaspa:qp6f50fv632mwfk9gjyrjxsvyvrzmxvm03s3vnjs8u5dwhe6sj9x5xlyumxry).
- **Their statement:** because of MiCA, they host the wallet and provide it as a service ([post](https://x.com/i/status/2115128191560077807)).
- The thread says what this proves: the data matches the anchored snapshot. Whether the physical origin is true still depends on accurate records ([post](https://x.com/i/status/2114735361175757194)).
- No public code repository or Odoo Apps listing was found on 27 Oct 2026. You can contact them via [rrmconsultoria.es/contacto](https://www.rrmconsultoria.es/contacto).

### Use cases for Odoo users
- **Supply-chain provenance:** batch-level links from raw materials to delivery, which helps with recalls and supplier disputes (as in RRM's example).
- **Timestamped audit trails:** tamper-evident hashes of records, useful for EU traceability and compliance evidence.
- **Cross-border invoicing and settlement:** a payment on a public network that both parties can verify, without a card processor in between.
- **Treasury and remittances between subsidiaries.**
- **Micropayments.**

Only the first two are shown in RRM's module. The rest are possibilities, not shipped Odoo features.

### Upcoming (not production-ready for Odoo today)
- **Tokenized assets, loyalty points and stablecoin-style tokens:** the fungible token standard KCC-20 has `Status: Final` ([kaspanet/kccs, kcc-0020.md](https://github.com/kaspanet/kccs)). Related KCC-23 is still Draft. Wallets and tooling are still catching up ([master file](https://github.com/STP-KAS/kaspa-master-file)).
- **Escrow and conditional payments:** covenants / spend rules are live ([docs.kaspa.org](https://docs.kaspa.org)), but developer tooling is still maturing.

## Why Kaspa

| Claim | Source |
|---|---|
| Fast blocks: 10 blocks per second since Crescendo (it was 1 before) | [KIP-14 (Active)](https://github.com/kaspanet/kips/blob/master/kip-0014.md), [kaspa.org/lore](https://kaspa.org/lore) |
| Low fees: about 0.001816 KAS for a batch of 50 certificates (RRM's figure, one data point) | [RRM post](https://x.com/i/status/2114734458679042480) |
| Proof-of-work security, fair launch, no premine | [kaspa.org](https://kaspa.org), [master file](https://github.com/STP-KAS/kaspa-master-file) |
| Independent verification: anyone can check a tx on public explorers, from any country | [explorer.kaspa.org](https://explorer.kaspa.org), [kaspa.stream](https://kaspa.stream) |
| No central payment processor: settlement is peer-to-peer on L1 | [kaspa.org](https://kaspa.org) |

## Sources and references
- Kaspa master file (community fact base): https://github.com/STP-KAS/kaspa-master-file
- kaspanet GitHub org: https://github.com/kaspanet. "Rusty Kaspa" is the Rust node, https://github.com/kaspanet/rusty-kaspa
- KIPs (Kaspa Improvement Proposals): https://github.com/kaspanet/kips. KCCs (token and contract conventions): https://github.com/kaspanet/kccs
- Core developers: Michael Sutton (lead protocol, [GitHub](https://github.com/michaelsutton), [X](https://x.com/michaelsuttonil)). For a fuller list, see the "Who" section of the master file and the kaspanet contributor graphs. "Core" is not a formal title.
- Research forum: https://research.kas.pa (Crescendo thread: https://research.kas.pa/t/crescendo-hardfork-discussion-thread/279)
- Kaspa site: https://kaspa.org · lore: https://kaspa.org/lore · docs: https://docs.kaspa.org · wiki: https://wiki.kaspa.org
- Explorers: https://explorer.kaspa.org · https://kaspa.stream
- Odoo: https://www.odoo.com · Source code (official repo): https://github.com/odoo/odoo · Apps: https://apps.odoo.com · payment providers: https://www.odoo.com/documentation/18.0/applications/finance/payment_providers.html
- RRM Consultoría: https://www.rrmconsultoria.es/ · announcement thread: https://x.com/rrm_consultoria/status/2114734310578376709

Not financial advice. No price talk.
