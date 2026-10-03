# Risk Register

A sample risk register demonstrating risk identification, assessment, treatment, and tracking — built from a WAPT finding (Day 4 of my cybersecurity training) and one physical-security scenario.

| Field | RISK-001 | RISK-002 |
|---|---|---|
| **Risk ID** | RISK-001 | RISK-002 |
| **Asset** | Customer invoice database/endpoint | Data stored on employee laptop |
| **Threat** | Attacker with basic knowledge of IDOR/broken access control | Anyone with physical access who could misuse an unattended device |
| **Vulnerability** | IDOR / BOLA on `/api/invoices/download?invoice_id=` | Laptop left unlocked and unattended in a public place |
| **Likelihood** | Medium — requires an authenticated account and some security knowledge to exploit | Low — requires someone to notice and act on the opportunity |
| **Impact** | High — could expose other customers' invoice and payment data | High — could expose sensitive files or allow malware installation |
| **Inherent Risk** | High | Medium |
| **Existing Controls** | Authentication enforced; authorization (ownership) check missing | Device lock feature available, but not enabled/used |
| **Risk Owner** | Application/Engineering Lead | Laptop owner |
| **Treatment Plan** | Assign fix to engineering; implement server-side authorization check validating resource ownership before returning data; monitor for several weeks post-fix | Inspect device for tampering/malware/data loss; enable automatic screen-lock timeout and full-disk encryption; reinforce security awareness |
| **Control** | Enforce authorization checks on the server side | Mandatory screen-lock timeout policy and physical security awareness for devices in public |
| **Residual Risk** | Low | Low |
| **Status** | Open | Open |

---

### Methodology Notes
- **Risk rating** follows a Likelihood × Impact matrix (Low/Medium/High/Critical), consistent with standard risk assessment practice.
- **Inherent Risk** reflects the risk level before any control is applied; **Residual Risk** reflects what remains after a control/fix is in place — rarely zero, since no control is perfect.
- **Treatment selection** follows the four standard options: Mitigate, Accept, Transfer, Avoid. Both entries above were treated via **Mitigate**, as the affected assets are actively used and the risks were too significant to accept, avoid, or transfer.
