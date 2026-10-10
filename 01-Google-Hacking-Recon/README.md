# Lab 01 — Passive Reconnaissance with Google Hacking

Assignment from the **Information Systems Security** course (UNESP Bauru, March 2026).
Goal: understand how publicly indexed information can be used in the **reconnaissance phase of a penetration test**, using advanced search operators (Google Dorks) to find exposed data, and propose mitigations.

**Authors:** Cristiano Marques Silva and Augusto Cavalari

> **Disclosure note:** this is a sanitized version of the original report. Real domains, URLs, screenshots and credentials were removed on purpose, because some findings involved live third-party systems. The original report is not published.

---

## Rules of engagement

The activity was strictly **passive reconnaissance**:

- ✅ Only Google searches and viewing content already indexed and public
- ❌ No login attempts, vulnerability testing, data downloads or interaction with admin pages
- ❌ If a page looked sensitive or private, stop and move on

The goal was to **observe and analyze, not exploit**.

## Methodology

Two public targets were analyzed: a foreign government web system and a Brazilian municipal government portal.
Searches combined these operators:

| Operator | Purpose |
|---|---|
| `site:` | Restrict results to a domain or top-level domain |
| `filetype:` / `ext:` | Find specific file types (`.txt`, `.pdf`, `.json`, ...) |
| `intitle:` | Match words in the page title |
| `inurl:` | Match words in the URL |
| `"..."` | Exact phrase |
| `-term` | Exclude noise (code-hosting mirrors, package registries) |

Excluding sites such as GitHub, GitLab and Hugging Face was key to removing thousands of irrelevant results and isolating files hosted on the target's own servers.

## Findings

### 1. Database credentials in a public text file — Critical

```
site:<country-TLD> filetype:txt "db_password" -github -gitlab -gitee -huggingface
```

- **What was found:** an indexed `.txt` file from a government system containing the database schema, the SQL commands that create the database user **including its password**, API structure and credentials for a cloud storage account.
- **Risk:** an attacker learns the internal architecture and may gain direct access to the database and cloud storage — a full data breach scenario.
- **Mitigation:** remove the file from the server, **rotate every exposed credential immediately**, request removal from Google's index, and keep secrets in environment variables or a secrets manager instead of plain-text files.

### 2. Directory listing enabled — High

```
site:<target-domain> intitle:"index of"
```

- **What was found:** the web server listed folder contents publicly, revealing internal paths and files that anyone could browse and download without authentication.
- **Risk:** exposure of backups (`.sql`), configuration files and logs; the file structure also reveals the technology stack to an attacker.
- **Mitigation:** disable directory listing (`Options -Indexes` on Apache, `autoindex off` on Nginx) and keep non-public files outside the web root.

### 3. Indexed administrative login page — Medium

```
site:.br inurl:admin -github -gitlab
```

- **What was found:** an administrative login page of a municipal government portal, not linked from the public website but indexed by Google.
- **Risk:** a known admin entry point is a target for credential stuffing, brute force and phishing against staff.
- **Mitigation:** restrict the admin area to the internal network or VPN (IP allowlist), require MFA, and add `noindex` so it does not appear in search results.

## Lessons learned

- **Search engines index what servers expose.** Most findings come from misconfiguration, not sophisticated attacks.
- **`robots.txt` is not a security control.** It asks search engines not to index a path, but it also tells attackers where to look.
- **Handling an exposed credential:** a security analyst who finds one should not test it. The correct path is **responsible disclosure** — notify the owner or the national CERT (e.g. CERT.br in Brazil) so the credential can be rotated.
- **Sanitize before publishing.** Even academic reports can leak live credentials; this version was rewritten for that reason.

## Skills demonstrated

- OSINT and passive reconnaissance (pentest phase 1)
- Building precise Google Dorks with operators and exclusions
- Risk assessment and severity classification of findings
- Proposing technical mitigations
- Ethical handling of sensitive data and responsible disclosure
