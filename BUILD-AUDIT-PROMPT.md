# GROK BUILD PROMPT — Audit STP-KAS/kaspa-odoo

**Scope:** only the repo https://github.com/STP-KAS/kaspa-odoo (branch `main`). Do not touch any other repo.

**Rules**
- Facts only. Every claim must have a source link, primary sources preferred: kaspanet repos (KIPs, KCCs, rusty-kaspa), kaspa.org, docs.kaspa.org, research.kas.pa, odoo.com docs, the official Odoo source repo https://github.com/odoo/odoo, the RRM Consultoría site and X thread, and https://github.com/STP-KAS/kaspa-master-file.
- No secrets, keys or tokens in any file or commit.
- No tags. No releases. No force push. Normal commits to `main` only.
- Do not post anywhere else, and do not contact RRM or Odoo.
- No price talk or hype.

**Task**
1. Read every file in the repo.
2. Verify every claim and every link against its primary source:
   - Open each URL and drop or fix dead ones.
   - Check the 10 BPS and Crescendo facts against KIP-14, and the KCC-20 Final and KCC-23 Draft status against kaspanet/kccs.
   - Check RRM's figures against their posts and the kaspa.stream address.
   - Check whether any public RRM repo or Odoo Apps listing exists by now.
3. Flag errors, overclaims, unsourced lines and outdated statements. Fix them in place, with sources.
4. Do your own research. Add what is missing and useful to Odoo users, sourced. Examples: how an Odoo payment provider module would accept KAS, wallet and custody options, the MiCA context, and existing Kaspa payment or merchant tools.
5. Replace the "Pending" line under `## Grok bots audit` → `### Grok Build audit` in README.md with:
   - **Opinion:** 3–5 lines.
   - **Findings:** a list, each with a source.
   - **Fixes made:** what you changed.
   - **Suggestions / additions:** what you added, and what remains open.
   - The date, and your commit SHA(s).
6. Commit with clear messages and push to `STP-KAS/kaspa-odoo` `main`.
7. If you are blocked (auth, access, a source you can't reach), stop and report. Never fake verification.

Quality over speed. Within this repo you have full authority.
