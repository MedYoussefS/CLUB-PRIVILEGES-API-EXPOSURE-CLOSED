# Security Vulnerability Report

**TITLE: Broken Object Level Authorization (BOLA) — Unauthenticated Emails Access via Partial URL**

---

**Reported by:** Mohamed Youssef
**Date:** 2026-05-12
**Contact:** medyoussef.it@gmail.com
**Severity:** HIGH
**Target:** `https://api.clubprivileges.app/view-email/00000/sendPathePass`

---

## Overview

A Broken Object Level Authorization (BOLA) vulnerability was identified in the view email API of the Club Privileges platform. This flaw allows any unauthenticated or unauthorized user to see email of **any customer** — past or future — simply by knowing or enumerating a sequential ticket ID.

---

## What I Found

The ticket download endpoint follows this URL pattern:

```
https://api.clubprivileges.app/view-email/{email_id}/sendPathePass
```

- `{email_id}` — A **sequential, predictable integer** (e.g., 60000, 60001, 60002)

```
https://api.clubprivileges.app/view-email/{email_id}/sendPathePass
```

This means authorization is **completely bypassed**. Since `{email_id}` is sequential, an attacker can iterate through IDs to see email of every user on the platform.

---

## How to Reproduce It

This is straightforward to verify:

**Step 1 — Go to the view email api URL**

```
https://api.clubprivileges.app/view-email/61000/sendPathePass
```

Here for exemple `61000` is the email ID (sequential)

**Step 2 — Enumerate other email**
Increment the ticket ID to access new tickets:

```
https://api.clubprivileges.app/view-email/61001/sendPathePass
https://api.clubprivileges.app/view-email/61002/sendPathePass
...
```

Each request returns a different customer's email without any authorization check.

---

## Why This Is Serious

This vulnerability has significant privacy, security, and business implications:

- **Ticket Fraud:** An attacker can download and present another user's valid QR code or barcode to gain unauthorized entry to screenings.
- **No Authentication Required:** The exploit requires zero credentials. It is accessible to any person on the internet with basic technical knowledge.
- **Predictable IDs Enable Automation:** Because email IDs are sequential integers, a simple script can be programed to get always the last email ID and collect it.
- **Future Emails Are Exposed:** emails not yet used — for upcoming sessions — are equally accessible, enabling targeted fraud before the legitimate owner even arrives.
- **Regulatory Risk:** Depending on jurisdiction, unauthorized exposure of customer personal data may constitute a violation of data protection regulations (e.g., GDPR in the European Union).

---

## A Note on How I Handled This

This vulnerability was discovered incidentally while using the application as a legitimate customer. Upon recognizing the security implication, I did **not** enumerate other users' emails, did not collect any personal data, and did not share or exploit the vulnerability beyond the minimal verification needed to confirm its existence.

This report is submitted in good faith through responsible disclosure. I am available to assist your security or engineering team in understanding, reproducing, or verifying the fix for this issue.

Please acknowledge receipt of this report and provide an estimated timeline for remediation. I am happy to agree on a disclosure timeline that gives your team adequate time to patch before any public disclosure.

---

## References

- [OWASP API Security Top 10 — API1:2023 Broken Object Level Authorization](https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/)
- [CWE-639: Authorization Bypass Through User-Controlled Key](https://cwe.mitre.org/data/definitions/639.html)
- [OWASP Testing Guide — Testing for Insecure Direct Object References](https://owasp.org/www-project-web-security-testing-guide/)

---

_This report was prepared in good faith for responsible disclosure purposes only._
_The researcher did not cause harm, access private data, or exploit this vulnerability beyond initial verification._
