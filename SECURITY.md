# Security Policy

This project handles assembly-line operator workflows for ISCO-08 8219
(Assemblers Not Elsewhere Classified). Treat vulnerabilities as
potentially high impact even when the demo data is synthetic — this
domain's failure modes include real pinch-point/crush hazard from
moving parts and fixtures and hand-tool injury, alongside physical
worker-safety risk generic to assembly-line work.

## Do Not Disclose Publicly

Report privately before opening public issues for:

- credential exposure
- real assembler, line or operator data exposure
- authorization bypass
- Assembly Line Scheduling Coordination Governor bypass
- audit-ledger tampering
- over-disclosure in reports or exports
- unsafe robot action dispatch
- any path that lets a proposal reach an assembly-execution decision,
  a line-safety-clearance decision, or a plant-safety-officer-override
  decision

## Reporting

Use GitHub private vulnerability reporting when available for the repository.
If that is unavailable, contact the repository maintainers through the
cloud-itonami organization before publishing details.

Include:

- affected commit or version
- reproduction steps
- expected and actual behavior
- impact on assembler/line data, policy enforcement or audit logging
- suggested fix, if known

## Production Guidance

- Store secrets outside Git.
- Keep real assembler/line/operator data outside this repository.
- Run policy tests before deployment.
- Export and review audit logs regularly.
- Use least privilege for operators and service accounts.
