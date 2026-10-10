# Lab 02 — Information Security Policy (Fictional Fintech)

Assignment from the **Information Systems Security** course (UNESP Bauru, March 2026).
Goal: write an Information Security Policy (ISP) for a fictional company, choosing the policy type and defining controls, incident handling, awareness training and penalties.

**Authors:** Cristiano Marques Silva and Augusto Cavalari

📄 **Full policy (Portuguese):** [security-policy-pt-br.pdf](security-policy-pt-br.pdf)

---

## Scenario: LegiãoPay

| | |
|---|---|
| Sector | Financial technology (online payments) |
| Size | ~50 employees (Board, IT, Customer Support, Finance, HR) |
| Endpoints | Standardized corporate laptops |
| Core systems | Payment app and database hosted entirely in the cloud |
| Office network | Corporate firewall, separate Wi-Fi for staff and guests |
| Work model | Hybrid — all remote access through a corporate VPN |

A fintech handles banking data and personal information, so a breach would hit customers directly and carry legal and reputational consequences.

## Policy type: logical

The policy focuses on **logical controls** — access to systems, networks, applications and data:

| Control | Rule |
|---|---|
| Access control | Least privilege: each employee gets access only to what their role requires |
| Authentication | Strong passwords and **mandatory MFA** for every corporate system; credentials are personal and non-transferable |
| Remote access | Sensitive data outside the office only through the approved corporate VPN |
| Software | Pirated, unlicensed or non-approved software is forbidden on corporate laptops |

## The CIA triad applied to a fintech

| Principle | Why it matters here | How it is enforced |
|---|---|---|
| **Confidentiality** | Banking data and transactions must stay private | Access control, authentication, encryption |
| **Integrity** | A modified transaction is a financial loss | Audit logs, data validation, version control |
| **Availability** | Customers expect payments to work at all times | Backups, redundancy, recovery plans, cloud infrastructure |

## Incident handling

1. **Detection** — 24/7 monitoring with firewalls and cloud security tools to spot anomalies and unauthorized access attempts.
2. **Response** — IT triggers the response protocol: **contain** the threat, **eradicate** the vulnerability, **recover** the affected systems.
3. **Root cause analysis** — every incident is analyzed to find what caused it.

## Awareness and training

- Mandatory security awareness training during onboarding.
- Periodic training, awareness campaigns and **phishing simulations**, since people are the first line of defense against social engineering.

## Penalties

When an incident results from negligence or a deliberate violation, HR conducts the disciplinary process. Penalties are proportional to severity: formal written warning, suspension, or dismissal for just cause under Brazilian labor law (CLT, art. 482).

## Skills demonstrated

- Security governance: translating business context into security rules
- Applying the CIA triad to a real-world sector
- Defining access-control and authentication requirements (least privilege, MFA, VPN)
- Structuring an incident response process (detect → contain → eradicate → recover)
