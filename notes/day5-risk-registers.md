# Day 5 — GRC + Practical Integration: Risk Registers

## PART A — Core Concepts

### Q1. What is a Risk Register, and why is it used?
A risk register is a structured record of an organization's identified risks and how they're being managed. It's used to track the full risk management workflow systematically, since relying on memory alone isn't realistic or defensible.

### Q2. Asset, Threat, Vulnerability
- **Asset:** Something valuable that needs to be protected.
- **Threat:** Something capable of causing harm or impact to the asset.
- **Vulnerability:** A weakness that can be exploited.

### Q3. Likelihood vs Impact
**Likelihood** is how probable it is that a risk is realized by exploiting a vulnerability.
*Example:* For the Day 4 IDOR finding, an attacker needs to be authenticated first, and needs some security knowledge to recognize and test for IDOR. Not every user could exploit it, but someone with relevant skills could — so Likelihood is **Medium**.

**Impact** is how bad the consequences would be if the risk materializes.
*Example:* For the same IDOR finding, if exploited, it could expose other customers' private data — so Impact is **High**.

### Q4. Inherent Risk vs Residual Risk
- **Inherent Risk:** The risk level in its original, unmitigated state — before any fix is applied.
  *Example:* Medium likelihood × High impact → Inherent Risk = High.
- **Residual Risk:** The risk level that remains **after** a control or fix has been applied. It's rarely zero, because no control is perfect.
  *Example:* After fixing the IDOR with a server-side authorization check, some small residual risk remains (e.g., possible edge cases or future regressions) — so Residual Risk = Low.

---

## PART B — Risk Treatment

### Q5. The four Risk Treatment options
Mitigate, Accept, Transfer, Avoid.

### Q6. Each option explained
- **Mitigate:** Take action to fix or reduce the risk. *Example: Fixing the IDOR with a server-side authorization check.*
- **Accept:** Consciously choose not to act, because the cost of fixing outweighs the potential impact — while still monitoring it. *Example: A very minor bug in a rarely-used internal tool.*
- **Transfer:** Shift responsibility for the risk to a third party. *Example: Buying cyber insurance, or using a vendor who assumes liability.*
- **Avoid:** Eliminate the risk entirely by removing the thing that causes it. *Example: Shutting down a vulnerable feature or endpoint entirely if it's not critical to the business, rather than trying to fix it.*

### Q7. Which treatment fits the Day 4 IDOR finding, and why?
**Mitigate.** Avoid isn't practical since the affected feature is used regularly by all customers. Accept isn't appropriate given the high severity — knowingly leaving a high-impact vulnerability unaddressed is irresponsible. Transfer doesn't apply, since the issue is in our own authorization logic, not something a third party controls. That leaves Mitigate: fix the authorization check and monitor afterward.

---

## PART C — Risk Rating

### Q8. Likelihood = Medium, Impact = High → Inherent Risk?
According to the severity matrix, Medium likelihood × High impact = **High** Inherent Risk.

### Q9. Why can't every IDOR automatically be called "Critical"?
Because severity depends on context: what data is exposed, whether it's a Read or Write/Modify IDOR, and which endpoint it affects. An IDOR exposing someone's order history is very different in impact from one exposing payment card data.

---

## PART D — Day 4 IDOR as a Risk Register Entry

| Field | Value |
|---|---|
| Risk ID | RISK-001 |
| Asset | Customer invoice database/endpoint |
| Threat | An attacker with basic knowledge of IDOR/broken access control |
| Vulnerability | IDOR / BOLA |
| Likelihood | Medium — exploitation requires an authenticated account and some security knowledge |
| Impact | High — could expose customers' payment details and invoice data |
| Inherent Risk | High |
| Existing Controls | Authentication is enforced; authorization (ownership) check is missing |
| Risk Owner | Application/Engineering Lead |
| Treatment Plan | Assign the fix to engineering, implement a server-side authorization check validating resource ownership before returning invoice data, and monitor for several weeks after deployment |
| Control | Enforce authorization checks on the server side |
| Residual Risk | Low |
| Status | Open |

---

## PART E — GRC + WAPT Integration

### Q11. How does a Day 4 WAPT finding become a GRC risk?
```
IDOR Finding
   ↓
Risk Assessment (determine Likelihood, Impact, Inherent Risk, Existing Controls)
   ↓
Risk Owner assigned
   ↓
Treatment Plan decided (in this case, Mitigate)
   ↓
Control implemented (server-side authorization check)
   ↓
Residual Risk calculated (Low — can't realistically reach zero)
   ↓
Tracking (status monitored in the risk register until closed)
```
The WAPT team finds and reports the vulnerability to the security team. GRC then performs the risk assessment, assigns an owner, and tracks the fix through to resolution and ongoing monitoring.

### Q12. Why is a Risk Register useful to an auditor?
A risk register is a structured, documented record of identified risks and how they're managed. When an auditor asks "prove you're managing risk," you can't just claim it verbally — you need solid evidence. The risk register serves as that evidence, showing the auditor exactly what risks exist, how they were assessed, and what's been done about them.

### Q13. Technical Vulnerability vs Business Risk
A **technical vulnerability** is a specific flaw in a system that can be exploited (e.g., IDOR). A **business risk** is the broader organizational impact, which doesn't have to be purely technical — e.g., no proper backup policy for customer data is a business risk, even though it isn't a "bug" in the traditional sense.

---

## PART F — Master Revision

- **Risk Register:** A structured record of identified risks and how they're being managed.
- **Inherent Risk:** The risk in its original, unmitigated state.
- **Residual Risk:** The risk remaining after a fix/control has been applied.
- **Mitigate:** Taking action to fix or reduce a risk.
- **Accept:** Knowingly choosing not to act on a risk, while monitoring it.
- **Transfer:** Shifting the risk to a third party (e.g., insurance, a vendor).
- **Avoid:** Eliminating the risk entirely by removing what causes it.

### Q15. Day 5 in one sentence
Day 5 was about how GRC identifies, assesses, treats, and tracks risk — from discovery of a vulnerability all the way to closure — using a structured record called a risk register.

---

## PRACTICAL DELIVERABLE — Risk Register (2 Rows)

### Row 1 — Day 4 IDOR Finding

| Field | Value |
|---|---|
| Risk ID | RISK-001 |
| Asset | Customer invoice database/endpoint |
| Threat | An attacker with basic knowledge of IDOR/broken access control |
| Vulnerability | IDOR / BOLA |
| Likelihood | Medium |
| Impact | High |
| Inherent Risk | High |
| Existing Controls | Authentication present; authorization check missing |
| Risk Owner | Application/Engineering Lead |
| Treatment Plan | Assign the fix, implement server-side authorization validation, monitor for several weeks post-fix |
| Control | Enforce authorization checks on the server side |
| Residual Risk | Low |
| Status | Open |

### Row 2 — Laptop Left Unlocked in a Public Place

| Field | Value |
|---|---|
| Risk ID | RISK-002 |
| Asset | Data stored on the laptop |
| Threat | Anyone with physical access who could misuse the unattended device |
| Vulnerability | Laptop left unlocked and unattended in a public place |
| Likelihood | Low — requires someone to notice and act on the opportunity |
| Impact | High — could expose sensitive files or allow malicious software installation |
| Inherent Risk | Medium (per the matrix: Low likelihood × High impact) |
| Existing Controls | Device lock feature available, but not enabled/used at the time |
| Risk Owner | Laptop owner |
| Treatment Plan | Inspect the device for signs of tampering, malware, or data loss; enable automatic screen-lock timeout and full-disk encryption; reinforce device security awareness |
| Control | Mandatory screen-lock timeout policy and physical security awareness for devices in public |
| Residual Risk | Low |
| Status | Open |

---
**Status: Day 5 complete ✅**
