# Day 4 — IDOR / Broken Access Control + SOC

## PART A — WAPT

### 1. What is IDOR?
IDOR stands for **Insecure Direct Object Reference**. It happens when a user can access another user's object (profile, order, document) just by changing an identifier in a request, and the server grants access without checking authorization. Example: I'm User A with profile ID 101. If I change the ID to 102 and the server returns User B's profile data, that's IDOR.

### 2. What is an "object" in IDOR?
An object is any piece of data or resource that belongs to someone — a user ID, invoice, order, document, photo, bank statement, ticket, address, booking, or file.

### 3. IDOR explained with the hotel-room analogy
A hotel has rooms 101, 102, 103, etc. When I book room 101, I get a key for room 101. A secure system checks: "Is this key specifically authorized for room 101?" A vulnerable system only checks: "Is this a valid hotel key?" — meaning my key might open *every* room. Similarly, in a web application, if I can access someone else's account just by tampering with an object identifier, that's IDOR.

### 4. Authentication vs Authorization
- **Authentication:** Verifying *who you are* (are you really who you claim to be when logging in?).
- **Authorization:** Verifying *what you're allowed to do or access* (does this specific data actually belong to you?).

### 5. Why does IDOR happen?
Because the application checks *who you are* (authentication) but forgets to check *whether you're authorized* to access the specific object being requested.

### 6. Normal vs vulnerable behavior
If User A legitimately owns orders 100, 103, and 106, and accesses them at different times, that's normal. If User A rapidly accesses orders 100–105 in quick succession, that's a vulnerable-looking pattern worth investigating — though it's not automatically proof of exploitation. It could be legitimate automation, admin activity, authorized security testing, or an actual attack. It needs investigation, not assumption.

### 7. Where object identifiers can appear
User ID, order ID, profile ID, invoice ID, address ID, booking ID, ticket ID, payment ID — in URLs, query parameters, POST bodies, and JSON payloads.

### 8. Are sequential/guessable IDs automatically an IDOR vulnerability?
No. Guessable IDs alone aren't the vulnerability — if the server properly checks authorization on every request, it doesn't matter that the ID is easy to guess. The vulnerability is the **missing authorization check**, not the ID format.

### 9. Can IDOR happen with UUIDs?
Yes. UUIDs make IDs much harder to guess, but if an attacker obtains a valid UUID through some other means (e.g., it's exposed elsewhere, or leaked), and the server still doesn't verify ownership, it's still vulnerable to IDOR.

### 10. Two-account manual testing methodology
Create Account A and Account B. Log in as A, note the object ID. Log in as B, note B's object ID. Then, as A, replace the ID in A's request with B's known ID. If the response returns B's data, that's IDOR. If access is denied, authorization is being enforced correctly.

### 11. Testing IDOR using Burp Suite Repeater
1. Create two test accounts.
2. Set up Burp Proxy and perform the relevant action in-browser to capture the request in **HTTP History**.
3. Send the request to **Repeater**.
4. Change the object identifier to the other account's known ID and resend.
5. If the response returns `200 OK` with the other user's data, IDOR is confirmed.

### 12. Why use two separate accounts/sessions when testing?
Three reasons: (1) you need a **known, legitimate** second ID to test against, rather than guessing at real strangers' private data; (2) having your own test account gives you **ground truth** — you know exactly what that account's data looks like, so you can confirm with certainty that you actually retrieved it; (3) it keeps testing ethical and avoids touching real users' information.

### 13. Is every IDOR equally serious?
No. Severity depends on what data is exposed. An IDOR on a food-ordering system that only reveals someone's last order is low severity — there's little an attacker can actually do with it. An IDOR that exposes payment information like card numbers is high severity, since that data has direct value to an attacker.

### 14. Read IDOR vs Write/Modify IDOR
- **Read IDOR:** You can only view another user's data.
- **Write/Modify IDOR:** You can actually change or delete another user's data — generally far more severe.

### 15. IDOR vs Broken Access Control
- **IDOR:** A specific vulnerability type where unauthorized access to data is gained by manipulating an object identifier.
- **Broken Access Control:** The broader category of vulnerabilities where users gain access they shouldn't have, through various methods. IDOR is just one type within this category.

### 16. If changing an ID only reveals that an object exists, is that automatically IDOR?
No. Merely confirming an object's existence (e.g., a "this ID is valid" vs. "not found" response) is not IDOR by itself — it's more like information disclosure. IDOR requires actually accessing the object's **data or contents** without authorization.

### 17. Correct remediation for IDOR
Enforce a server-side authorization check on every request — verify that the logged-in user actually owns or has rights to the specific object being requested, not just that the ID is valid.

### 18. Why must authorization be enforced server-side?
Because anything on the client side (browser, app) can be modified or bypassed by an attacker. The server is the only place that can be trusted to make the final decision.

---

## PART B — SOC

### 19. How can IDOR abuse appear in access/application logs?
Logs may show a single user rapidly accessing many different object IDs in a short time window, or — if the logs capture ownership — a user accessing objects that don't belong to their account.

### 20. Log patterns that might make a SOC analyst suspicious
A high volume of requests to different object IDs in a short period, cross-user access visible in logs (if captured), and large sequences of requests (hundreds) to an ID-based endpoint.

### 21. Why are sequential requests to many object IDs suspicious?
Because this isn't normal user behavior — a real user doesn't typically browse through hundreds of sequential records quickly. This pattern suggests automated enumeration, which is abnormal and worth investigating.

### 22. Why are application-level logs useful for detecting IDOR?
Because they can directly show that User A accessed User B's specific data — something network-level logs alone can't reveal. Without this detail, a SOC analyst has to investigate much more manually, which is slower and harder.

### 23. If a user requests 100 sequential object IDs, does that automatically prove an attack?
No. It's suspicious and worth investigating, but it could also be legitimate application automation, admin activity, authorized security testing, or an actual IDOR exploit. A SOC analyst investigates first and only labels it an attack once confirmed.

### 24. Suspicious signal vs confirmed malicious activity
A suspicious signal (like 100 rapid requests) raises a flag but isn't proof on its own. It becomes confirmed malicious activity once evidence shows the user actually accessed another user's private data without authorization.

---

## PART C — WAPT + SOC CONNECTION

### 25. The complete connection between WAPT and SOC for an IDOR vulnerability
WAPT discovers and reports the vulnerability to the security team. SOC then analyzes the logs to confirm exploitation — checking if User A actually accessed User B's data without authorization. If confirmed, SOC/security team directs developers to fix it by enforcing server-side authorization checks. Once patched, the entire process (finding, confirmation, fix, verification) is documented to serve as an **evidence/audit trail** for compliance purposes.

### 26. Testing `GET /api/invoices/download?invoice_id=5521` for IDOR
Capture the request in Burp Suite. Create a second test account and identify its corresponding invoice ID. Send Account A's request to Repeater, then change the `invoice_id` parameter to Account B's known invoice ID and resend. If the response returns `200 OK` with Account B's invoice data, that confirms an IDOR vulnerability.

### 27. What result would prove the invoice endpoint has an authorization vulnerability?
Successfully viewing another user's actual invoice data by changing the `invoice_id` parameter — not just getting a `200` response, but confirmed access to data that doesn't belong to you.

---

## PART D — REPORTING

### 28. What should an IDOR vulnerability report contain?
Title, vulnerability name, impact, steps to reproduce, expected result, actual result, remediation guidance, and evidence/proof of remediation once fixed.

### 29. Remediation for an IDOR vulnerability
Enforce server-side authorization checks on every request to object-based endpoints.

### 30. What sensitive information should you avoid putting into a public GitHub note/report?
Real personal data belonging to others whose information was exposed through the vulnerability — no real names, account details, or actual leaked data should ever appear in a public write-up.

---

## MASTER MEMORY TEST

### 31. Core definitions
- **Authentication:** Verifying who you are — typically happens at login.
- **Authorization:** Verifying what you're allowed to access or do — assigns access based on user permissions.
- **IDOR:** A broken access control vulnerability where a user accesses another user's data by changing an object identifier, gaining unauthorized access.
- **Threat:** Something capable of causing harm.
- **Vulnerability:** A weakness that can potentially be exploited.
- **Risk:** The potential for harm when a threat exploits a vulnerability, impacting an asset.

### 32. IDOR in one sentence (for a beginner)
IDOR is a vulnerability where an application lets you access another user's data just by changing an ID in a request, without checking if you're authorized to see it.

### 33. IDOR in one sentence (for an interviewer)
IDOR is a broken access control vulnerability where an attacker gains unauthorized access to another user's data by manipulating an object reference, such as an ID, that the server fails to validate ownership of.

### 34. The ONE thing to remember when testing for IDOR
Being able to *change* an object identifier is not IDOR by itself — IDOR is confirmed only when changing that identifier actually grants unauthorized access to someone else's data.

---
**Status: Day 4 complete ✅**
