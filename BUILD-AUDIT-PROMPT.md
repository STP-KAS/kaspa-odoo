# GROK BUILD PROMPT — Audit and extend STP-KAS/kaspa-odoo

**Scope:** only https://github.com/STP-KAS/kaspa-odoo, branch `main`. Pull first and continue from the current README. Do not rewrite it from scratch, and keep its factual, neutral tone.

**Rules**
- Facts only. Every statement must have a source, preferably a primary one: github.com/odoo/odoo (file paths, branches 18.0/19.0), odoo.com docs, kaspanet repos (KIPs, KCCs, rusty-kaspa), kaspa.org, docs.kaspa.org, research.kas.pa, and https://github.com/STP-KAS/kaspa-master-file.
- Professional and neutral. No marketing, no emotion, no price talk.
- No secrets. No tags or releases. No force push. Only this repo. Do not post anywhere.
- Never fake verification. If you are blocked (auth, access, an unreachable source), stop and report.

**Task**
1. **Odoo module comparison.** Verify every row and file link in "Comparison with Odoo modules" against the odoo/odoo source. Extend it where useful, for example:
   - Odoo's `payment` provider interface: what a KAS provider would need to implement.
   - Currency rounding and decimals in `res.currency` for an 8-decimal asset.
   - Whether `inter_company`, `sign` and `documents` are Enterprise-only, with an odoo.com source.
   - Any public third-party crypto payment modules on apps.odoo.com.

   Correct the coverage labels if needed.
2. **Stablecoin outlook.** Verify and extend the stablecoin item: a Kaspa dollar, multiple stablecoins, a Tether-like issuer. Keep it future, not current. Report only what has a primary source, say who stated it and when, and mark everything else "not confirmed".
3. **Hard fork note.** Verify the name and status of the next hard fork, currently given as DAGKnight per kaspa.org/lore. Check the rusty-kaspa `dagknight` branch, KIPs and research.kas.pa. No dates without a source.
4. Re-check every URL in the repo and fix or drop dead links.
5. Replace the text under `## Grok bots audit` → `### Grok Build audit` with your own take:
   - **Opinion:** 3–5 lines.
   - **Findings:** each with a source.
   - **Fixes made.**
   - **Suggestions / additions.**
   - The date and your commit SHA(s).

   Keep the "TN10 ops2 view" part unchanged.
6. Commit with clear messages and push to `main`.
