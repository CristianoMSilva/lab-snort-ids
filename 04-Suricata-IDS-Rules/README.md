# Lab 04 — Suricata IDS: Custom Detection Rules

Hands-on lab from the **Information Systems Security** course (UNESP Bauru, August 2026).
Goal: extend the previous IDS lab by writing, testing and tuning custom rules that detect suspicious behavior in a controlled network, then analyze the alerts as a SOC analyst would.

**Authors:** Cristiano Marques Silva and Augusto Cavalari

▶ **Video demo (all four rules firing live):** [YouTube](https://youtu.be/cC-I5FPFk10)

📄 **Full report (Portuguese):** [report-pt-br.pdf](report-pt-br.pdf)

---

## Environment

Two virtual machines (VirtualBox) on an isolated **Host-Only** network:

| Role | OS | IP |
|---|---|---|
| Monitored host + IDS | Linux Mint 22.3 (Xfce, 64-bit) | `192.168.100.1/24` |
| Traffic generator ("attacker") | Linux Mint 22.3 (Xfce, 64-bit) | `192.168.100.2/24` |

- **IDS:** Suricata 8.0.6 in passive detection mode (IDS, not IPS)
- **Logs:** `fast.log` (one-line alerts) and `eve.json` (structured JSON events)
- **Rules:** [`suricata-ssi.rules`](suricata-ssi.rules)

## Test cases

| # | Event | Rule logic | Traffic generated (host 2) | Result |
|---|---|---|---|---|
| 1 | Ping | ICMP with `itype:8` (echo request only) | `ping -c 4 192.168.100.1` | ✅ Alert per request |
| 2 | Port scan | TCP `SYN`, ≥15 from same source in 5 s | `sudo nmap -sS -F 192.168.100.1` | ✅ Alerts during scan |
| 3 | SSH brute force | TCP `SYN` to port 22, ≥5 from same source in 10 s | `for i in {1..8}; do nc -zv -w 1 192.168.100.1 22; done` | ✅ Alerts after threshold |
| 4 | Suspicious URL | HTTP inspector, `http.uri` contains `/admin` | `curl http://192.168.100.1/admin` | ✅ Alert on request |

For test 4, a simple web server (`sudo python3 -m http.server 80`) ran on host 1 so the HTTP request had a target; it answered `404`, but the request was still detected.

### Sample alerts (`fast.log`)

```
08/25/2026-16:51:38.724115 [**] [1:1000001:1] [SSI] Requisicao de ping (ICMP) detectada [**] [Classification: (null)] [Priority: 3] {ICMP} 192.168.100.2:8 -> 192.168.100.1:0
08/25/2026-16:51:50.320695 [**] [1:1000002:1] [SSI] Suspeita de varredura de portas (port scan) [**] [Classification: (null)] [Priority: 3] {TCP} 192.168.100.2:62136 -> 192.168.100.1:1720
08/25/2026-16:51:50.321233 [**] [1:1000002:1] [SSI] Suspeita de varredura de portas (port scan) [**] [Classification: (null)] [Priority: 3] {TCP} 192.168.100.2:62136 -> 192.168.100.1:1110
```

In the port scan alerts, the **source port stays fixed (62136) while the destination port changes** on every line — the typical fingerprint of an `nmap` SYN scan.

### Fields an analyst would use

From `eve.json`: timestamp (event correlation), source IP (who), destination IP and port (what was targeted), protocol and TCP flags (attack vector), and `signature_id` (which rule fired).

## False positives and tuning

| Rule | Possible false positive | Suggested improvement |
|---|---|---|
| Ping | Legitimate monitoring tools (Zabbix, Nagios) ping constantly | Whitelist monitoring IPs or add a rate threshold |
| Port scan | Load balancers or apps opening many connections at once | Tune `count`/`seconds` to the network's baseline, or use Suricata's built-in scan detection |
| SSH brute force | Automation tools (e.g. Ansible) opening many SSH sessions | Whitelist management IPs; inspect failed-auth responses instead of only SYN packets |
| `/admin` | Authorized administrators accessing the panel | Alert only when the response is `401`/`403` (`http.stat_code`) |

No false positives occurred during the tests, but the rules overlap: SYN packets sent to port 22 also count toward the port-scan threshold, so an SSH burst can raise both alerts. In production, using `type: both` or `limit` in the threshold would also reduce repeated alerts for the same event.

## IDS vs. IPS

Suricata ran in **IDS mode**: it inspected a copy of the traffic and only generated alerts — nothing was blocked. In **IPS mode** it would sit inline and could `drop` or `reject` malicious packets before they reach the target, at the cost of adding a point of failure and latency to the network.

## Problems solved during setup

1. **Mismatched subnets** — the IDS VM started in NAT mode (`10.0.2.15`) while the generator was on `192.168.100.0/24`. Fixed by assigning a static IP on the Host-Only interface.
2. **Wrong interface in `suricata.yaml`** — the default config used `eth0`, but the NIC was `enp0s3`. Fixed in the `af-packet` section, then restarted the service.
3. **Service not starting in a live session** — the OS ran from live media, so Suricata did not start automatically. Started it with `systemctl start suricata` and validated the config with `suricata -T`.

## Limitations observed

- Encrypted traffic (HTTPS/TLS) cannot be inspected without TLS interception.
- Real-time analysis is memory-intensive.
- Detection quality depends entirely on well-tuned rules; loose thresholds flood analysts with alerts.

## Skills demonstrated

- Writing and validating IDS signatures (ICMP, TCP flags, thresholds, HTTP inspection)
- Generating attack traffic with `nmap`, `nc` and `curl` in an isolated lab
- Reading and interpreting `fast.log` and `eve.json` alerts
- False-positive analysis and rule tuning
- Troubleshooting network and IDS configuration

---

← Previous: [Lab 03 — Snort 3 IDS with Docker](../03-Snort-IDS-Docker/)
