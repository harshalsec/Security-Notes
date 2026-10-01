# Day 3 — GRC Fundamentals

**Track:** GRC  
**Topic:** What is GRC, and Why Does It Exist?

---

## 1. What is GRC?

**GRC** stands for **Governance, Risk, and Compliance**.

- **Governance** establishes security rules, responsibilities, accountability, and decision-making.
- **Risk** is the possibility that a threat could exploit a vulnerability and cause harm or impact to an asset.
- **Compliance** means meeting applicable requirements and being able to provide evidence that those requirements are being followed.

### Easy Memory Trick

> **GRC = Decide + Prioritize + Prove**

- **Governance = Decide**
- **Risk = Prioritize**
- **Compliance = Prove**

---

## 2. What is Governance?

**Governance** is the system that establishes how security decisions are made within an organization.

It defines:

- Security rules
- Responsibilities
- Accountability
- Decision-making authority
- Security expectations

> **Governance asks: “Who decides what, and who is responsible?”**

Governance is broader than simply creating rules. It also includes ownership and oversight.

---

## 3. What is Risk?

**Risk** is the possibility that a threat could exploit a vulnerability and cause an impact to an asset.

Example:

- **Asset:** Customer information
- **Threat:** Attacker
- **Vulnerability:** IDOR/BOLA
- **Possible impact:** Unauthorized access to customer information

A simple risk model is:

> **Risk = Likelihood × Impact**

This is a basic mental model. Real organizations may use more detailed risk-assessment methodologies.

> **Risk asks: “What could go wrong, how likely is it, and how bad would it be?”**

---

## 4. What is Compliance?

**Compliance** means meeting applicable laws, regulations, standards, contractual requirements, and organizational requirements.

It also involves providing **evidence** that required controls or processes are actually being followed.

For example, if an organization's policy requires MFA for corporate accounts, having the policy alone is not enough. The organization should be able to demonstrate that MFA has actually been implemented and is being used.

> **Compliance asks: “Are we meeting the applicable requirements, and can we prove it?”**

---

## 5. What is an Asset?

An **asset** is anything valuable to an organization that needs protection.

Examples:

- Customer data
- Employee information
- Databases
- Websites
- Applications
- Servers
- Credentials
- Financial information
- Intellectual property

> **Asset = Something valuable that needs protection.**

---

## 6. What is a Threat?

A **threat** is something capable of causing harm to an asset.

Examples include:

- An attacker
- Malware
- A malicious insider
- Natural events
- Accidental actions
- Other harmful events or circumstances

> **Threat = Something that can cause harm.**

**Important:** A threat is not always a person. An attacker can be a **threat actor**, while a threat can also refer more broadly to a harmful event, source, or circumstance.

---

## 7. What is a Vulnerability?

A **vulnerability** is a weakness or condition in a system that could potentially be exploited by a threat.

Examples:

- IDOR/BOLA
- SQL injection
- Weak authentication
- Missing security controls
- Misconfigured systems
- Outdated software

> **Vulnerability = A weakness that can be exploited.**

---

## 8. Risk Formula

A simple risk formula is:

> **Risk = Likelihood × Impact**

Where:

- **Likelihood** = How likely is the harmful event to happen?
- **Impact** = How bad would the result be if it happened?

> **Likelihood = How often / how likely?**  
> **Impact = How bad?**

---

## 9. Policy vs Procedure

### Policy

A **policy** is a high-level organizational rule that states what must or must not be done.

Example:

> **Policy:** All employees must use MFA for corporate accounts.

### Procedure

A **procedure** explains the step-by-step process for implementing or following a policy or control.

Example:

> **Procedure:** Open the organization's identity-management portal → select the employee account → enable MFA → register the approved authentication method → verify MFA → document completion.

> **Policy = WHAT must be done.**  
> **Procedure = HOW to do it.**

---

## 10. Control vs Procedure

### Control

A **control** is a safeguard or measure used to reduce or manage security risk.

Example:

> **Control:** MFA is required for administrator accounts.

### Procedure

A **procedure** is the step-by-step process used to implement, operate, or maintain the control.

Example:

> **Procedure:** Follow the organization's documented steps to enable MFA, enroll the administrator's approved authentication method, verify it works, and record the implementation.

> **Control = Protection/Safeguard.**  
> **Procedure = Steps used to operate the protection.**

**Important correction:** A control is not simply a rule. A control is a safeguard or measure that helps prevent, detect, or reduce risk. Policies can require controls.

---

## 11. Law vs Standard

### Law

A **law** is a legally enforceable requirement established through a country's legal system.

### Standard

A **standard** is a defined set of requirements, specifications, or practices used as a benchmark.

A standard is **not automatically a law**. However, a standard can become mandatory when it is required by a law, regulation, contract, or organizational policy.

### Examples

- **ISO/IEC 27001** → Standard
- **NIST Cybersecurity Framework (CSF)** → Framework, not a standard
- **CIS Controls** → A set of security controls/safeguards

> **Law = Legal requirement.**  
> **Standard = Defined benchmark/requirements.**

---

## 12. What Does “Evidence” Mean in Compliance?

**Evidence** is information or documentation that demonstrates that an organization has implemented and is following required controls, processes, or other applicable requirements.

Suppose an organization's policy requires all employees to use MFA for corporate accounts.

The organization may need to demonstrate that the requirement is actually being followed.

Possible evidence includes:

- MFA configuration records
- Identity-management reports
- Access-control records
- Audit logs
- Security assessment reports
- Penetration-testing reports
- Policies and procedures
- Review records
- Training records
- Other relevant documentation

### Important Point

Having a policy saying **“MFA is required”** does not automatically prove that MFA is actually enabled.

> **Policy = What the organization says should happen.**  
> **Evidence = Proof of what was actually implemented or performed.**

---

## 13. If WAPT Finds an IDOR, Which GRC Concepts Can We Connect to the Finding?

Suppose a WAPT tester finds an **IDOR/BOLA vulnerability** in a customer portal.

### Step 1 — Identify the Asset

The **customer information and customer accounts** are valuable assets.

### Step 2 — Identify the Threat

An **attacker** could act as a threat actor who attempts to exploit the vulnerability.

### Step 3 — Identify the Vulnerability

The **IDOR/BOLA vulnerability** is the weakness that may allow unauthorized access to another user's data or resources.

### Step 4 — Identify the Risk

If the vulnerability can be exploited, an attacker may gain unauthorized access to customer information.

The risk assessment considers:

- Likelihood of exploitation
- Potential impact
- Number/type of assets affected
- Business consequences
- Required remediation priority

### Step 5 — Governance

Governance determines:

- Who owns the finding?
- Who is responsible for fixing it?
- What security policies apply?
- What security decisions need to be made?
- What remediation process should be followed?

### Step 6 — Controls

The organization may use appropriate security controls such as:

- Proper server-side authorization checks
- Strong access-control mechanisms
- Security testing before release
- Monitoring and logging
- Secure development practices

### Step 7 — Compliance

The organization checks whether any applicable legal, regulatory, contractual, or internal requirements are relevant to the affected data and system.

### Step 8 — Evidence

The organization can maintain relevant evidence such as:

- WAPT/pentest report
- Remediation records
- Security-test results
- Access-control configuration
- Relevant logs
- Policies and procedures
- Review or audit records

### Complete Mental Model

> **WAPT finds the vulnerability → Risk determines the potential business impact → Governance assigns responsibility and makes decisions → Controls reduce the risk → Compliance checks applicable requirements → Evidence demonstrates what was implemented and done.**

---

# Mistakes I Made and What I Learned

## 1. Compliance is not only about following company policies

My original answer focused mainly on whether employees follow organizational policies.

### Correction

Compliance can involve:

- Laws
- Regulations
- Standards
- Contracts
- Organizational policies
- Other applicable requirements

> **Compliance = Meeting applicable requirements + being able to demonstrate it with evidence.**

---

## 2. A control is not the same thing as a rule

I originally described a control mainly as a rule.

### Correction

A **control is a safeguard or measure that helps prevent, detect, or reduce risk.**

Example:

- **Policy:** All administrator accounts must use MFA.
- **Control:** MFA enforced on administrator accounts.
- **Procedure:** Steps used to enroll an administrator and configure MFA.

---

## 3. Risk is more than simply “something bad could happen”

My original definition was close, but risk should connect the potential event to **likelihood and impact**.

### Better mental model

> **Risk = What could go wrong + How likely it is + How bad the impact could be.**

Simplified formula:

> **Risk = Likelihood × Impact**

---

## 4. A threat is not always a person

I initially used “attacker” as the definition of a threat.

### Correction

An attacker can be a **threat actor**, but a threat can be broader than a person.

Examples include:

- Threat actors
- Malware
- Accidental actions
- Natural events
- Harmful circumstances

---

## 5. Governance is broader than creating security rules

I correctly identified rules, responsibilities, and accountability, but governance also includes:

- Decision-making
- Ownership
- Oversight
- Accountability
- Security direction

> **Governance = Who decides what, who owns it, and how it is managed.**

---

## 6. A standard is not automatically mandatory

I originally said standards are requirements that need to be followed.

### Correction

A standard is not automatically legally mandatory.

It may become mandatory because it is:

- Required by law or regulation
- Required by a contract
- Adopted by an organization
- Required by another applicable obligation

---

## 7. NIST CSF is a framework, not a standard

This is an important terminology correction.

- **ISO/IEC 27001** → Standard
- **NIST Cybersecurity Framework (CSF)** → Framework
- **CIS Controls** → Security controls/safeguards

---

## 8. Evidence is not just “documents”

Evidence can include many types of information:

- Logs
- Configuration records
- Reports
- Policies
- Procedures
- Screenshots
- Access reviews
- Test results
- Audit records

The key question is:

> **Can we demonstrate that the required thing was actually implemented or performed?**

---

# Day 3 Final Memory Map

```text
                    GRC
                     |
        +------------+------------+
        |            |            |
   GOVERNANCE       RISK      COMPLIANCE
     DECIDE       PRIORITIZE      PROVE
        |            |            |
 Who decides?   What could      Are we meeting
 Who owns it?   go wrong?       requirements?
 What are the   How likely?    Can we prove it?
 rules?         How bad?
        |
     POLICY
        |
      WHAT
        |
     CONTROL
        |
    SAFEGUARD
        |
   PROCEDURE
        |
       HOW
```

## Security Finding Memory Chain

```text
WAPT
  ↓
Finds Vulnerability
  ↓
Identify Asset
  ↓
Identify Threat
  ↓
Assess Risk
  ↓
Governance
  ↓
Assign Responsibility
  ↓
Apply Controls
  ↓
Check Compliance
  ↓
Maintain Evidence
```

## One-Line Revision

> **Governance decides, Risk prioritizes, Controls reduce risk, Compliance checks requirements, and Evidence proves what was done.**
