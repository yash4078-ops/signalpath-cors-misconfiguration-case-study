# signalpath-cors-misconfiguration-case-study
Security case study documenting CORS misconfiguration on app.signalpath.com. Responsible disclosure report, research methodology, and impact analysis included.
# CORS Misconfiguration Case Study – app.signalpath.com

## Overview
This repository documents a real-world security research case study involving a **CORS (Cross-Origin Resource Sharing) misconfiguration** identified on **app.signalpath.com**, a healthcare-related web application operated by **Verily Life Sciences**.

The vulnerability allowed arbitrary `Origin` values to be reflected in the `Access-Control-Allow-Origin` response header, violating Same-Origin Policy protections and enabling potential cross-origin data exfiltration.

> **Disclosure Status:** Reported responsibly via HackerOne  
> **Program:** Verily Life Sciences  
> **Report Outcome:** Closed as *Informative*  
> **Research Purpose:** Security learning, validation, and portfolio demonstration

---

## Vulnerability Summary

- **Vulnerability Type:** CORS Misconfiguration  
- **CWE:** CWE-942 – Permissive Cross-domain Policy with Resource Inclusion  
- **Affected Asset:** `https://app.signalpath.com`  
- **Severity Assessment:** High (based on industry standards)

---

## Root Cause

The server reflects **arbitrary Origin headers** without performing proper validation or allowlisting.

Origin: https://evil.com


### Vulnerable Response:

Access-Control-Allow-Origin: https://evil.com

Vary: Origin


This behavior enables malicious websites to read cross-origin responses when credentials are included.

---

## Proof of Misconfiguration

### Request

GET /api/v1/studies/1 HTTP/2
Host: app.signalpath.com
Origin: https://attacker.com


### Response

HTTP/2 200 OK
Access-Control-Allow-Origin: https://attacker.com

Content-Type: text/html


✔ Arbitrary origin reflected  
✔ Consistent behavior across multiple endpoints  
✔ No origin validation observed

---

## Confirmed Endpoints

- `/api/v1/studies/1`
- `/api/v1/studies`
- `/api/v1/user/profile` *(behavior consistent)*

All tested endpoints exhibited the same CORS behavior.

---

## Demonstrated Impact (Unauthenticated)

- Full Single Page Application (SPA) source code disclosure
- Internal module paths leaked (import maps)
- Application architecture and API structure exposed
- Technology stack and dependency information revealed

This significantly reduces attacker reconnaissance effort.

---

## Potential Impact (Authenticated Users)

If exploited while a victim is logged in:

- Cross-origin exfiltration of sensitive data
- Unauthorized API access using victim session
- Exposure of PHI / PII in healthcare workflows
- High risk of regulatory non-compliance (HIPAA, GDPR)

---

## Real-World Attack Scenario

1. Victim is logged into `app.signalpath.com`
2. Victim visits attacker-controlled website
3. Malicious JavaScript executes cross-origin request
4. Server reflects malicious origin
5. Browser allows response to be read
6. Sensitive data is exfiltrated silently

---

## Proof of Concept (PoC)

A working HTML/JavaScript PoC was developed to demonstrate:

- Automatic cross-origin request execution
- Credential inclusion
- Data exfiltration simulation

> **PoC hosted via GitHub Gist (HTMLPreview)**  
> *(Link intentionally omitted from README to avoid misuse)*

---

## HackerOne Disclosure Summary

- **Submission Date:** December 20, 2025  
- **Platform:** HackerOne  
- **Program:** Verily Life Sciences  
- **Status:** Informative  

### Analyst Feedback (Summary)
The report was closed as *Informative* due to lack of authenticated exploitation, though the CORS reflection behavior itself was acknowledged.

---

## Why This Case Study Matters

According to OWASP, PortSwigger, and HackTricks:

- Reflecting arbitrary origins is a **security misconfiguration**
- Authentication is not required to prove misconfiguration
- Impact assessment depends on application context
- Healthcare applications demand stricter controls

This repository exists to demonstrate **methodology, validation, and responsible disclosure**, not exploitation.

---

## References

- OWASP CORS Security Guide  
- PortSwigger – CORS Vulnerabilities  
- CWE-942  
- HackTricks – CORS Misconfiguration  

---

## Disclaimer

This repository is published **strictly for educational and portfolio purposes**.  
No active exploitation is performed.  
All research followed responsible disclosure practices.

---

## Researcher

**Sachin Mishra**  
Independent Security Researcher  
