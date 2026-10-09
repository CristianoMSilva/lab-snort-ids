# Lab 03 — Snort 3 IDS with Docker

Hands-on lab from the **Information Systems Security** course (UNESP Bauru, June 2026).
Goal: install and configure an Intrusion Detection System (IDS) in an isolated environment, write a custom rule, generate test traffic with `ping`, and observe how the IDS detects it.

**Author:** Cristiano Marques Silva (individual assignment)

▶ **Video walkthrough (Portuguese):** [YouTube](https://youtu.be/-uW6wJ0Vy7M)

---

## Environment

| Component | Details |
|---|---|
| Host | Windows + Docker Desktop |
| Container | `alpine:latest` (lightweight Linux) |
| IDS | Snort 3 (Snort++ 3.9.2), installed via `apk` |
| Monitored interface | `lo` (container loopback) |
| Mode | Passive detection (IDS) — traffic is logged, not blocked |

## Files

| File | Purpose |
|---|---|
| [`docker-compose.yml`](docker-compose.yml) | Creates the `ids-snort` container, installs Snort on startup and mounts the rule file into it |
| [`mysnort.rules`](mysnort.rules) | Custom detection rule (see below) |

## The rule

```
alert icmp any any -> any any (msg:"ALERTA: PING detectato!"; sid:1000001; rev:1;)
```

| Part | Meaning |
|---|---|
| `alert` | Action: generate an alert when the rule matches |
| `icmp` | Protocol to inspect (the protocol used by `ping`) |
| `any any -> any any` | Any source IP/port to any destination IP/port |
| `msg:"..."` | Text shown in the alert (Portuguese: "ALERT: PING detected!") |
| `sid:1000001` | Signature ID; values ≥ 1,000,000 are reserved for local/custom rules |
| `rev:1` | Rule revision number |

## How to run

**1. Start the container** (from this folder):

```bash
docker compose up -d
```

**2. Start Snort** with the custom rule, printing alerts to the console:

```bash
docker exec -it ids-snort snort -v -i lo -R /etc/snort/rules/mysnort.rules -A alert_fast
```

- `-i lo`: listen on the loopback interface
- `-R`: load the custom rule file
- `-A alert_fast`: one-line alert format

**3. Generate traffic** in a second terminal:

```bash
docker exec -it ids-snort ping -c 4 127.0.0.1
```

## Result

Snort raised an alert for each ICMP packet:

```
06/09-15:51:16.121903 [**] [1:1000001:1] "ALERTA: PING detectato!" [**] [Priority: 0] {ICMP} 127.0.0.1 -> 127.0.0.1
06/09-15:51:16.121927 [**] [1:1000001:1] "ALERTA: PING detectato!" [**] [Priority: 0] {ICMP} 127.0.0.1 -> 127.0.0.1
06/09-15:51:17.122418 [**] [1:1000001:1] "ALERTA: PING detectato!" [**] [Priority: 0] {ICMP} 127.0.0.1 -> 127.0.0.1
...
```

Each line shows the timestamp, the rule that fired (`1:1000001:1` = generator:SID:revision), the message, the protocol and the source → destination addresses.

**Observation:** alerts come in pairs about 25 µs apart. Because the rule matches *any* ICMP packet, both the **echo request** and the **echo reply** trigger it, so 4 pings produce 8 alerts.

## Limitations and next steps

- **Single host:** traffic was generated inside the same container on the loopback interface. A realistic setup separates the IDS from the traffic generator.
- **Broad rule:** matching every ICMP packet doubles the alerts. Restricting it to echo requests (`itype:8`) and adding a threshold would reduce noise.
- **Detection only:** Snort ran as an IDS, so packets were logged but never blocked (IPS mode would require inline deployment).

These points are addressed in [Lab 04 — Suricata IDS rules](../04-Suricata-IDS-Rules/), which uses two virtual machines, an `itype:8` ping rule, and new rules for port scans, SSH brute force and suspicious URLs.

## Skills demonstrated

- Networking fundamentals: ICMP and traffic analysis
- Defensive security: writing and testing IDS signatures
- Containerization: reproducible lab environment with Docker Compose
