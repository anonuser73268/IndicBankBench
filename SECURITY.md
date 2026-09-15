# Security Policy

## Reporting a vulnerability

Do not disclose a suspected vulnerability in a public issue. Use the anonymous repository's
[private security-advisory form](https://github.com/anonuser73268/IndicBankBench/security/advisories/new).
No security-reporting email address is published during anonymous review.

Include a concise description, impact, affected version, and safe reproduction steps. Never
include real banking credentials, customer data, or production transcripts.

## Supported versions

| Version | Supported |
| --- | --- |
| Anonymous review snapshot | yes |

## Scope

- The evaluation harness under `indicbankbench/harness/` and `indicbankbench/scripts/`.
- Prompt-injection or jailbreak behavior that compromises benchmark integrity.
- Accidental inclusion of real credentials or personal data in the synthetic case release.
