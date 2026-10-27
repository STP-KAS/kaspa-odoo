# GROK BUILD PROMPT — Independent audit of STP-KAS/kaspa-odoo

**Scope:** only https://github.com/STP-KAS/kaspa-odoo, branch `main`. Pull first. Your output goes in your own section, `## Grok bots audit` → `### Grok Build audit` in README.md. Do not edit the "TN10 ops2 view" part, and keep the factual, neutral tone throughout.

**Goal:** repeat TN10 ops2's exercise independently. Open every source yourself and form your own conclusions. Do not rely on the README's wording as evidence.

**Rules**
- Facts only. Every statement needs a source link, preferably a primary one.
- Professional and neutral. No marketing, no emotion, no price talk.
- No secrets. No tags or releases. No force push. Only this repo. Do not post anywhere, and do not contact RRM, Odoo or the Kaspa Dollar team.
- Never fake verification. If you are blocked (auth, access, an unreachable source), stop and report it in your section.

**Sources to go through (all of them)**
1. RRM Consultoría: the X thread https://x.com/rrm_consultoria/status/2114734310578376709 and its replies, the site https://www.rrmconsultoria.es/, and the batch address on https://kaspa.stream that they cite.
2. Odoo:
   - The source code at https://github.com/odoo/odoo, branches 18.0 and 19.0, with this as an analysis of the actual code rather than a skim: `addons/payment` and `payment_*`, `account` (multi-currency, reconciliation, the `inalterable_hash` / secure entries), `account_edi_ubl_cii`, `account_peppol`, `stock` lots and the traceability report, and `mrp`. Also check whether `inter_company`, `sign` and `documents` exist.
   - The docs at https://www.odoo.com/documentation and the app store at https://apps.odoo.com, including any crypto payment modules.
3. Kaspa: https://kaspa.org and https://kaspa.org/lore, https://docs.kaspa.org, https://wiki.kaspa.org, https://research.kas.pa, and the kaspanet repos rusty-kaspa (including the `dagknight` branch), kips (KIP-14) and kccs (KCC-20 and KCC-23 status). Also check `consensus/core/src/constants.rs` in rusty-kaspa (`SOMPI_PER_KASPA`) for the glossary.
4. The Kaspa master file: https://github.com/STP-KAS/kaspa-master-file
5. Kaspa Dollar:
   - The X account https://x.com/KaspaDollar and its site https://kaspadollar.com.
   - Alexander Säfström: X https://x.com/asaefstroem and GitHub https://github.com/saefstroem.
   - Michael Sutton: X https://x.com/michaelsuttonil.
   - Check the status, the mechanism, the fundraising figures and any whitepaper or code.
6. Every other URL in the README, including the explorers.

**Task**
1. Build your own version of the Odoo comparison, using only the binary labels "Gap" or "Handled by Odoo". Where Odoo handles something locally and Kaspa would add an external proof, label it "Handled by Odoo" and state the addition. Note every row where you disagree with the README.
2. Check every claim in Summary, What, Relevant properties, Outlook (DAGKnight, stablecoins, the Kaspa Dollar side note) Glossary and Sources. Check that the Summary matches the body and adds nothing unsourced, and that the glossary definitions are correct (for example, the 8-decimal sompi unit and the Toccata covenants). Mark each one as confirmed, wrong or unsourced, with your source.
3. Check every URL and list any that are dead.
4. Write your section using these parts:
   - **Opinion:** 3–5 lines.
   - **Summary and glossary check:** whether the Summary is accurate, modest and consistent with the body, and whether each glossary entry is correct and readable for Odoo engineers new to Kaspa. Suggest changes, with sources.
   - **Comparison:** your table.
   - **Findings:** each with a source.
   - **Corrections needed:** for each, the claim, why it's wrong, and the source.
   - **Suggestions / additions:** with sources.
   - **Sources checked:** a list, each with the date you checked it.
   - The date and your commit SHA(s).

   You may also fix clear factual errors elsewhere in the README. If you do, list every such edit under "Fixes made" in your section.
5. Commit with clear messages and push to `main`.

Quality over speed. You have full authority within this repo.
