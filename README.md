# Enterprise SOC Detection Engineering Lab

A 32-sprint enterprise SOC lab built to develop practical experience in Windows security monitoring, Splunk, detection engineering, MITRE ATT&CK, correlation, threat hunting, incident response, detection tuning, SOC operations, vulnerability management, and purple-team validation.

---

## Project Status

```text
32 / 32 Sprints Complete
```

**Final Capstone:** CAP-001 — End-to-End SOC Purple-Team Validation

---

## Project Objective

The objective of this project was to build a functioning SOC environment and progressively mature it from basic infrastructure and telemetry collection into a complete detection and response lifecycle.

The project evolved through:

```text
Infrastructure
      |
      v
Telemetry Collection
      |
      v
Detection Engineering
      |
      v
MITRE ATT&CK Mapping
      |
      v
Correlation
      |
      v
Incident Response
      |
      v
Detection Tuning
      |
      v
Threat Hunting
      |
      v
Detection Opportunity Development
      |
      v
SOC Operations
      |
      v
Vulnerability Management
      |
      v
Purple-Team Validation
```

---

## Lab Environment

| System | Role |
|---|---|
| DC01 | Windows Server Domain Controller / DNS |
| WIN11-01 | Windows 11 Test / Source Workstation |
| WIN11-02 | Windows 11 Monitored Endpoint |
| SIEM01 | Splunk Enterprise |
| corp.lab | Active Directory Domain |

The lab uses Windows security telemetry, PowerShell logging, Sysmon where applicable, Splunk Universal Forwarders, and Splunk Enterprise for centralized monitoring and analysis.

---

## SOC Data Flow

```text
Windows Endpoints
      |
      v
Windows Security Logs
PowerShell Logs
Sysmon
      |
      v
Splunk Universal Forwarder
      |
      v
Splunk Enterprise
      |
      +---------------------+
      |                     |
      v                     v
Detections             Threat Hunting
      |                     |
      v                     v
Correlations        Detection Opportunities
      |                     |
      +----------+----------+
                 |
                 v
          Incident Response
                 |
                 v
          Detection Tuning
                 |
                 v
            SOC Dashboard
```

---

## Detection Portfolio

### DET-001 — Network Port Scan

```text
MITRE ATT&CK:
T1046 — Network Service Discovery
```

---

### DET-002 — Repeated Failed Authentication

```text
MITRE ATT&CK:
T1110 — Brute Force
```

---

### DET-003 — Encoded PowerShell Execution

```text
MITRE ATT&CK:
T1059.001 — PowerShell
```

---

### DET-004 — Suspicious Scheduled Task Creation

```text
MITRE ATT&CK:
T1053.005 — Scheduled Task/Job: Scheduled Task
```

---

### DET-005 — Privileged Domain Group Membership Change

```text
MITRE ATT&CK:
T1098.007 — Account Manipulation
```

---

### DET-006 — Suspicious Remote Network Logon

```text
MITRE ATT&CK:
T1021 — Remote Services
```

DET-006 was formally tuned after investigation demonstrated that Event ID 4624 alone was too broad.

---

## Correlation Rules

### CORR-001 — Multi-Stage Windows Attack Activity

Correlates multiple independent detection categories into a higher-confidence multi-stage incident.

Conceptual sequence:

```text
Authentication Activity
       |
       v
Remote Access
       |
       v
Execution
       |
       v
Persistence
       |
       v
Privilege Modification
```

---

### CORR-002 — Remote Network Logon Followed by PowerShell

Correlates:

```text
4624 / Logon Type 3
        |
        | <= 600 Seconds
        v
4104 / PowerShell
```

on the same endpoint.

MITRE coverage:

```text
T1021
+
T1059.001
```

---

## Threat Hunting

### HUNT-001 — Suspicious Windows Post-Authentication Activity

Hypothesis:

```text
Unauthorized authentication may be followed by execution,
persistence, or privilege activity even when no individual
event independently triggers an alert.
```

HUNT-001 produced:

```text
HF-001
```

which led to:

```text
DET-OPP-001
```

and ultimately:

```text
CORR-002
```

---

## Detection Opportunity Lifecycle

```text
HUNT-001
    |
    v
HF-001
    |
    v
DET-OPP-001
    |
    v
Correlation Engineering
    |
    v
CORR-002
```

This demonstrates how proactive threat hunting can produce new detection content.

---

## Incident Response

### INC-001 — Multi-Stage Windows Attack Investigation

Used CORR-001 as the basis for a formal retrospective investigation.

The investigation included:

- Authentication review
- Remote-access review
- PowerShell analysis
- Persistence analysis
- Privilege-change analysis
- Timeline reconstruction
- Evidence-gap documentation
- Incident disposition
- Detection tuning identification

---

### INC-002 — CAP-001 Purple-Team Validation

Final capstone incident.

Disposition:

```text
Detection Result:
TRUE POSITIVE

Disposition:
BENIGN POSITIVE — AUTHORIZED PURPLE-TEAM VALIDATION
```

---

## Detection Tuning

### TUNE-001 — DET-006 Refinement

Original condition:

```spl
EventCode=4624
```

Problem:

```text
Event ID 4624 represents many legitimate successful logons.
```

Refined logic:

```spl
EventCode=4624 LogonType=3
```

with local/empty source exclusions.

Result:

```text
TUNE-001:
CLOSED
```

This changed DET-006 into:

```text
Suspicious Remote Network Logon
```

rather than overstating the telemetry as confirmed administrative SMB activity.

---

## SOC Operations

### SOC-OPS-001 — SOC Operations & Detection Health Dashboard

Splunk dashboard providing visibility into:

- Reporting hosts
- Telemetry freshness
- Authentication activity
- Remote network logons
- PowerShell activity
- Privileged changes
- Detection-relevant events
- Host activity
- MITRE ATT&CK coverage

---

## Runbooks

### RUNBOOK-001 — SOC Shift Start

Defines a repeatable analyst workflow for:

```text
Telemetry Health
       |
       v
Authentication Review
       |
       v
Remote Access Review
       |
       v
PowerShell Review
       |
       v
Privilege Review
       |
       v
Anomaly Investigation
```

---

## Vulnerability Management

### VM-001 — Vulnerability Management Workflow

Defines:

```text
Asset Identification
       |
       v
Assessment
       |
       v
Finding
       |
       v
Validation
       |
       v
Risk Assessment
       |
       v
Remediation
       |
       v
Retest
       |
       v
Closure
```

---

### VULN-001 — Overly Permissive SMB Share

Controlled High-severity configuration finding.

Original state:

```text
Everyone — Full Control
```

The finding was:

- Discovered
- Validated remotely
- Risk rated
- Remediated
- Retested
- Closed

Final status:

```text
CLOSED
```

---

## Final Purple-Team Capstone

### CAP-001 — End-to-End SOC Purple-Team Validation

CAP-001 exercised:

```text
Remote Network Authentication
        |
        v
PowerShell Execution
        |
        v
Scheduled Task Persistence
        |
        v
Privileged Group Modification
```

The exercise validated:

- Telemetry collection
- Detection engineering
- CORR-002
- Threat hunting
- Incident response
- SOC dashboard visibility
- Vulnerability-remediation persistence
- Cleanup and closure procedures

Overall result:

```text
SUCCESS
```

---

## MITRE ATT&CK Coverage

| Analytic | Tactic | Technique |
|---|---|---|
| DET-001 | Discovery | T1046 |
| DET-002 | Credential Access | T1110 |
| DET-003 | Execution | T1059.001 |
| DET-004 | Persistence | T1053.005 |
| DET-005 | Privilege Escalation / Persistence | T1098.007 |
| DET-006 | Lateral Movement | T1021 |
| CORR-001 | Multi-Stage | Multiple |
| CORR-002 | Lateral Movement + Execution | T1021 + T1059.001 |

---

## Repository Structure

```text
docs/
├── sprints/
├── detections/
├── correlations/
├── incidents/
├── incident-response/
├── tuning/
├── threat-hunting/
├── detection-opportunities/
├── soc-operations/
├── runbooks/
├── vulnerability-management/
├── vulnerabilities/
└── capstone/

screenshots/
├── sprint-01/
├── ...
└── sprint-32/
```

Only directories containing actual project artifacts are maintained.

---

## Skills Demonstrated

### Security Operations

- SOC monitoring
- Alert triage
- Incident investigation
- Incident disposition
- Analyst runbook development
- Telemetry health monitoring

### Detection Engineering

- SPL development
- Windows Event ID analysis
- Detection creation
- Positive validation
- Negative validation
- Correlation engineering
- Risk scoring
- Detection tuning
- False-positive reduction

### Windows Security

- Active Directory
- Windows Server
- Windows 11
- Windows Security Event Logs
- PowerShell logging
- Sysmon
- Domain authentication
- Group membership auditing
- Scheduled tasks
- SMB/network authentication

### SIEM

- Splunk Enterprise
- Splunk Universal Forwarder
- Saved searches
- Alerts
- Dashboards
- Event correlation
- Timeline analysis

### Threat Detection Frameworks

- MITRE ATT&CK mapping
- Hypothesis-driven threat hunting
- Cross-telemetry investigation
- Detection opportunity development

### Vulnerability Management

- Asset inventory
- Configuration assessment
- Finding validation
- Risk assessment
- Remediation
- Retesting
- SLA tracking
- Finding closure

### Purple Team

- Controlled attack simulation
- Detection validation
- Correlation validation
- Threat-hunt validation
- Incident-response validation
- Remediation regression testing

---

## Known Lab Limitations

This project intentionally documents technical limitations rather than hiding them.

Known limitations include:

- Event IDs 4625 and 4698 have demonstrated inconsistent historical availability.
- Some detection thresholds are lab-specific.
- DET-006 identifies remote network authentication but does not independently prove SMB administrative-share access.
- CORR-002 currently uses broad PowerShell Event ID 4104 activity at the correlation layer.
- The host system is resource constrained.
- The lab is intentionally isolated from general Internet access.
- SOC-OPS-001 reflects Splunk-ingested telemetry and is not a complete sensor-health platform.
- The lab is designed for controlled security engineering and learning rather than production use.

---

## Project Outcome

The project progressed from basic infrastructure into a complete SOC workflow:

```text
Infrastructure
      |
      v
Telemetry
      |
      v
Detection Engineering
      |
      v
MITRE ATT&CK
      |
      v
Correlation
      |
      v
Incident Response
      |
      v
Detection Tuning
      |
      v
Threat Hunting
      |
      v
Detection Opportunity
      |
      v
SOC Operations
      |
      v
Vulnerability Management
      |
      v
Purple-Team Validation
```

# 32 / 32 SPRINTS COMPLETE
