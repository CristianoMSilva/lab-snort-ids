# Information Security Labs

Hands-on security labs from the **Information Systems Security** course at **UNESP Bauru** (Computer Science, 2026), covering the full defensive cycle: reconnaissance, security governance, and intrusion detection with alert analysis.

Built with a **SOC (Security Operations Center)** perspective: write detection rules, generate controlled attack traffic, read the logs, and evaluate false positives.

---

## Labs

| # | Lab | Focus | Tools | Authors |
|---|---|---|---|---|
| 01 | [Passive Reconnaissance with Google Hacking](01-Google-Hacking-Recon/) | OSINT, pentest recon | Google Dorks | Cristiano & Augusto |
| 02 | [Information Security Policy](02-Security-Policy/) | Governance, CIA triad, incident response | — | Cristiano & Augusto |
| 03 | [Snort 3 IDS with Docker](03-Snort-IDS-Docker/) | IDS setup, first detection rule | Snort 3, Docker Compose | Cristiano |
| 04 | [Suricata IDS: Custom Detection Rules](04-Suricata-IDS-Rules/) | Rule writing, alert analysis, tuning | Suricata 8, nmap, netcat, curl | Cristiano & Augusto |

Labs 03 and 04 build on each other: Lab 03 detects a single ping inside a container; Lab 04 moves to two virtual machines and detects port scans, SSH brute force and suspicious HTTP requests, with false-positive analysis.

## Video demos

- ▶ [Snort 3 + Docker — ping detection](https://youtu.be/-uW6wJ0Vy7M) (Portuguese)
- ▶ [Suricata — four custom rules firing live](https://youtu.be/cC-I5FPFk10)

## Skills

| Area | What was done |
|---|---|
| **Reconnaissance** | Built Google Dorks to find exposed credentials, open directories and admin panels; classified risk and proposed mitigations |
| **Governance** | Wrote a security policy for a fintech: least privilege, MFA, VPN, incident response and awareness training |
| **Detection engineering** | Wrote Snort and Suricata signatures using protocol matching, TCP flags, thresholds and HTTP inspection |
| **Alert analysis** | Interpreted `fast.log` and `eve.json`, identified scan patterns and evaluated false positives |
| **Lab infrastructure** | Isolated environments with Docker and VirtualBox (Host-Only network); troubleshooting of interfaces and services |
| **Ethics** | Passive-only recon, sanitized publication and responsible disclosure practices |

## Further reading

📝 [Pentest: a arte da invasão ética e o caminho para a segurança da informação](https://medium.com/@criscristimarsilva/pentest-a-arte-da-invas%C3%A3o-%C3%A9tica-e-o-caminho-para-a-seguran%C3%A7a-da-informa%C3%A7%C3%A3o-2745ce6fc018) — my article on Medium (Portuguese)

---

**Note:** READMEs are in English; the original reports for labs 02 and 04 are included in Portuguese (PDF).

**Author:** [Cristiano Marques Silva](https://github.com/CristianoMSilva) · Co-author of labs 01, 02 and 04: Augusto Cavalari
