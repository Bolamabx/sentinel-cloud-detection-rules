# Microsoft Sentinel — Cloud Detection Rules (KQL)

**Portfolio Evidence Artifact — Project 05: Cloud IR Playbooks & SIEM Detection Engineering**

This repository contains sanitized, demonstrative KQL detection rules deployed in Microsoft Sentinel as part of a cloud-native incident response capability buildout for a regulated financial institution. All 18 rules were mapped to MITRE ATT&CK for Cloud (IaaS) and tuned against 30 days of historical log data before production deployment.

---

## Context

The institution had no cloud telemetry ingested into its SIEM and no cloud-specific detection capability. Following a near-miss ransomware event in a cloud workload identified only through third-party notification, a full cloud IR capability was mandated. This repository represents the detection engineering output of that program.

**Results achieved:**
- 65% reduction in mean time to detect (MTTD) for cloud security events
- 40% of high-confidence alerts auto-remediated via SOAR (Logic Apps)
- All 5 red team attack scenarios successfully detected
- FFIEC examination gap formally closed

---

## Repository Structure

```
sentinel-cloud-detection-rules/
├── kql/
│   ├── CLOUD-001-anomalous-s3-getobject-volume.kql
│   ├── CLOUD-002-cloudtrail-logging-disabled.kql
│   ├── CLOUD-003-iam-access-key-created-outside-workflow.kql
│   ├── CLOUD-004-ec2-instance-type-changed-to-gpu.kql
│   ├── CLOUD-005-mass-s3-delete-outside-window.kql
│   ├── CLOUD-006-azure-ad-impossible-travel.kql
│   ├── CLOUD-007-iam-policy-modified-by-non-admin.kql
│   └── CLOUD-008-root-account-activity-detected.kql
└── README.md
```

---

## MITRE ATT&CK Coverage Map

| Rule ID | Rule Name | Tactic | Technique |
|---------|-----------|--------|-----------|
| CLOUD-001 | Anomalous S3 GetObject volume | Collection / Exfiltration | T1530 / T1537 |
| CLOUD-002 | CloudTrail logging disabled | Defense Evasion | T1562.008 |
| CLOUD-003 | IAM access key created outside workflow | Persistence | T1098.001 |
| CLOUD-004 | EC2 instance type changed to GPU class | Impact | T1496 |
| CLOUD-005 | Mass S3 DeleteObject outside maintenance window | Impact | T1485 |
| CLOUD-006 | Azure AD sign-in from impossible travel location | Initial Access | T1078.004 |
| CLOUD-007 | IAM policy modified by non-admin principal | Privilege Escalation | T1098 |
| CLOUD-008 | Root account API activity detected | Initial Access | T1078.004 |

---

## Log Sources Required

All rules query one or more of the following data connectors in Microsoft Sentinel:

| Connector | Table | Rules |
|-----------|-------|-------|
| AWS CloudTrail | AWSCloudTrail | CLOUD-001 through 005, 007, 008 |
| Azure Active Directory | SigninLogs | CLOUD-006 |
| AWS GuardDuty | AWSGuardDuty | Enrichment |
| AWS Security Hub | AWSSecurityHub | Enrichment |

---

## Rule Design Standards

Each rule follows these standards applied across the detection program:

**1. MITRE ATT&CK first** — every rule maps to a specific technique before the KQL is written. Detection logic is derived from the technique's data sources and sub-technique indicators.

**2. Threshold tuning** — all thresholds (event counts, time windows, volume limits) were established by analyzing 30 days of baseline CloudTrail data to minimize false positives while maintaining detection sensitivity.

**3. Known-good exclusions** — each rule includes a watchlist or condition block to exclude approved automation accounts, CI/CD service principals, and scheduled maintenance windows. These exclusions are maintained as Sentinel Watchlists in production.

**4. Severity classification** — alert severity follows a consistent framework: Critical (immediate SOAR response), High (analyst triage within 1 hour), Medium (analyst triage within 4 hours), Low (review within 24 hours).

**5. False positive documentation** — each rule file documents known false positive scenarios and tuning decisions made during the baseline period.

---

## Frameworks Referenced

- MITRE ATT&CK for Enterprise v14 — Cloud IaaS platform
- NIST SP 800-61 — Computer Security Incident Handling Guide
- NIST CSF — Detect function
- AWS Security Best Practices
- FFIEC Cybersecurity Assessment Tool — Domain 3 (Cybersecurity Controls)

---

## Disclaimer

All rules in this repository are sanitized demonstrative artifacts for portfolio purposes. Threshold values, watchlist references, workspace IDs, subscription IDs, and all identifying information have been anonymized with placeholder values. These rules reflect real-world detection logic tuned for AWS CloudTrail and Azure AD telemetry. They are not production configurations of any specific organization.

**Author:** Bola Mabawonku | CISM · CRISC · CISSP · CCSP | [bolamabawonku.com](https://bolamabawonku.com)
