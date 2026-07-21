# Microsoft Purview DLP Lab 02
## DLP Detection and Policy Enforcement

![Microsoft Purview](https://img.shields.io/badge/Microsoft-Purview-0078D4?style=for-the-badge&logo=microsoft)
![Microsoft 365](https://img.shields.io/badge/Microsoft_365-E5-5E5E5E?style=for-the-badge&logo=microsoft)
![OneDrive](https://img.shields.io/badge/OneDrive-Protected-0078D4?style=for-the-badge&logo=microsoftonedrive)
![Microsoft Teams](https://img.shields.io/badge/Microsoft_Teams-Tested-6264A7?style=for-the-badge&logo=microsoftteams)
![DLP](https://img.shields.io/badge/Data_Loss_Prevention-Enforced-success?style=for-the-badge)

---

## Table of Contents

- Overview
- Objective
- Lab Environment
- Business Scenario
- Solution Architecture
- Test Workflow
- DLP Policy Configuration
- Investigation Workflow
- Results
- Screenshots
- Skills Demonstrated
- Learning Outcome

---

# Overview

This lab demonstrates how Microsoft Purview Data Loss Prevention (DLP) protects sensitive information stored in Microsoft 365.

A DLP policy was configured to detect Credit Card Numbers in OneDrive. During testing, a PDF containing sensitive payment card information was uploaded and shared through Microsoft Teams.

Microsoft Purview successfully:

- Detected sensitive information
- Applied DLP policy
- Displayed Policy Tip
- Restricted unauthorized access
- Generated security alert
- Logged the activity inside Activity Explorer

---

# Objective

Validate the complete Microsoft Purview DLP enforcement lifecycle by performing an end-to-end test from file upload through security investigation.

---

# Lab Environment

| Component | Technology |
|-----------|------------|
| Data Security | Microsoft Purview |
| Tenant | Microsoft 365 E5 Developer |
| Storage | OneDrive for Business |
| Collaboration | Microsoft Teams |
| Investigation | Activity Explorer |
| Alerting | Purview Alerts |

---

# Business Scenario

An employee accidentally attempts to share a document containing customer credit card information.

The organization has implemented Microsoft Purview DLP to prevent unauthorized disclosure of payment card information.

The policy automatically detects sensitive information and blocks unauthorized sharing.

---

# Solution Architecture

```text
                     Microsoft Purview DLP

          Upload Sensitive File
                    │
                    ▼
            OneDrive for Business
                    │
                    ▼
          Sensitive Information Scan
                    │
        Credit Card Number Detected
                    │
                    ▼
          DLP Policy Evaluation
                    │
      ┌─────────────┼─────────────┐
      ▼             ▼             ▼
Policy Tip     Restrict Access   Generate Alert
                                      │
                                      ▼
                           Activity Explorer
                                      │
                                      ▼
                           Security Investigation
```

---

# Test Workflow

1. Upload PDF containing Credit Card Numbers.
2. Store file in OneDrive.
3. Share file through Microsoft Teams.
4. Microsoft Purview scans the document.
5. Credit Card Number detected.
6. DLP policy triggered.
7. Policy Tip displayed.
8. Unauthorized access blocked.
9. Security alert generated.
10. Activity Explorer records the event.

---

# DLP Policy Configuration

| Setting | Value |
|----------|-------|
| Sensitive Information Type | Credit Card Number |
| Location | OneDrive |
| Policy Mode | Enforced |
| User Notification | Enabled |
| Policy Tip | Enabled |
| Restrict Access | Enabled |
| Alert Generation | Enabled |

---

# Investigation Workflow

```
File Upload
      │
      ▼
Sensitive Data Detected
      │
      ▼
Policy Enforcement
      │
      ▼
Alert Generated
      │
      ▼
Security Analyst Investigation
      │
      ▼
Activity Explorer Timeline
```

---

# Results

The policy successfully detected sensitive information and enforced organizational security controls.

### Successful Validation

- Sensitive information detected
- Policy matched
- Policy Tip displayed
- Access restricted
- Teams sharing prevented
- Alert generated
- Activity Explorer logged event
- Security investigation available

---

# Screenshots

## 1. Policy Synchronization

![Policy Sync](screenshots/01-policy-sync-completed.png)

---

## 2. Upload Test File

![Upload File](screenshots/02-upload-test-file.png)

---

## 3. File Uploaded Successfully

![File Uploaded](screenshots/03-file-uploaded-successfully.png)

---

## 4. Share File through Microsoft Teams

![Teams Share](screenshots/04-share-file-teams.png)

---

## 5. Policy Tip Displayed

![Policy Tip](screenshots/05-policy-tip-displayed.png)

---

## 6. Teams Sharing Attempt

![Teams Sharing](screenshots/06-teams-sharing-attempt.png)

---

## 7. Access Restricted

![Access Restricted](screenshots/07-access-restricted.png)

---

## 8. DLP Alert Generated

![Alert](screenshots/08-dlp-alert-generated.png)

---

## 9. Alert Investigation

![Alert Investigation](screenshots/09-alert-investigation.png)

---

## 10. Activity Explorer

![Activity Explorer](screenshots/10-activity-explorer.png)

---

## 11. DLP Rule Details

![Rule Details](screenshots/11-dlp-rule-details.png)

---

# Skills Demonstrated

- Microsoft Purview DLP
- Microsoft 365 Security
- OneDrive Protection
- Microsoft Teams Integration
- Sensitive Information Detection
- DLP Policy Enforcement
- Security Incident Investigation
- Activity Explorer Analysis
- Alert Triage
- Security Operations
- Data Protection

---

# Learning Outcome

This lab demonstrates an end-to-end Microsoft Purview DLP enforcement workflow—from detecting sensitive information during file sharing to automatically enforcing security controls, generating alerts, and enabling investigation through Activity Explorer.

The implementation reflects how enterprise security teams use Microsoft Purview to protect confidential business data within Microsoft 365 environments.
