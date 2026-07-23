# 🏥 Lab 06 – Microsoft Purview Custom Sensitive Information Type (SIT) & Data Loss Prevention (DLP)

<p align="center">

![Microsoft Purview](https://img.shields.io/badge/Microsoft-Purview-0078D4?style=for-the-badge&logo=microsoft)
![DLP](https://img.shields.io/badge/Data%20Loss%20Prevention-DLP-blue?style=for-the-badge)
![Information Protection](https://img.shields.io/badge/Information%20Protection-Custom%20SIT-success?style=for-the-badge)
![Exchange Online](https://img.shields.io/badge/Exchange-Online-0078D4?style=for-the-badge)
![OneDrive](https://img.shields.io/badge/OneDrive-Protected-0364B8?style=for-the-badge&logo=microsoftonedrive)
![Microsoft Teams](https://img.shields.io/badge/Microsoft-Teams-6264A7?style=for-the-badge&logo=microsoftteams)

</p>

---

# 📑 Table of Contents

- Overview
- Business Scenario
- Objectives
- Architecture
- Technologies Used
- Implementation
  - Step 1 – Create Custom Sensitive Information Type
  - Step 2 – Configure Detection Pattern
  - Step 3 – Test Custom SIT
  - Step 4 – Create DLP Policy
  - Step 5 – Deploy Policy
  - Step 6 – Validation
- Alert Investigation
- Results
- Skills Demonstrated
- Learning Outcomes

---

# 📖 Overview

Enterprise organizations often store proprietary identifiers that are unique to their business and cannot be detected using Microsoft's built-in Sensitive Information Types.

In this lab, I designed and implemented a **Custom Sensitive Information Type (SIT)** to identify **Hospital Medical Record Numbers (MRNs)** and enforced protection using **Microsoft Purview Data Loss Prevention**.

The policy successfully detected sensitive medical records and prevented unauthorized sharing through:

- Exchange Online
- Microsoft Teams
- OneDrive

The implementation was validated using Microsoft Purview investigation tools including:

- Activity Explorer
- Content Explorer
- DLP Alerts

---

# 🏥 Business Scenario

Contoso Regional Medical Center maintains confidential patient records containing proprietary Medical Record Numbers (MRNs).

Security requirements:

- Detect hospital MRNs
- Prevent accidental sharing
- Block emails containing patient records
- Block Teams file sharing
- Monitor OneDrive uploads
- Generate administrator alerts
- Enable investigation through Microsoft Purview

---

# 🎯 Objectives

✔ Create a Custom Sensitive Information Type

✔ Build custom Regular Expression detection

✔ Configure contextual keyword matching

✔ Create enterprise DLP policy

✔ Protect Exchange Online

✔ Protect Microsoft Teams

✔ Protect OneDrive

✔ Investigate DLP alerts

✔ Validate Content Explorer detections

---

# 🏗 Architecture

```text
                    Patient Registry Document
                              │
                              ▼
              Custom Sensitive Information Type
         (Regex + Healthcare Keywords + Confidence)
                              │
                              ▼
                   Microsoft Purview DLP
                              │
        ┌──────────────┬───────────────┬──────────────┐
        │              │               │              │
        ▼              ▼               ▼              ▼
   Exchange         OneDrive       Teams Chat    SharePoint
        │              │               │
        ▼              ▼               ▼
   Block Email    Monitor Upload   Block Sharing
        │              │               │
        └──────────────┴───────────────┘
                       ▼
               DLP Alert Generated
                       ▼
        Activity Explorer • Content Explorer
```

---

# 💻 Technologies Used

| Technology | Purpose |
|------------|----------|
| Microsoft Purview | Information Protection |
| Microsoft Purview DLP | Data Loss Prevention |
| Custom Sensitive Information Types | Proprietary Data Detection |
| Exchange Online | Email Protection |
| OneDrive | File Protection |
| Microsoft Teams | Collaboration Protection |
| Activity Explorer | Event Investigation |
| Content Explorer | Sensitive Data Discovery |

---

# ⚙ Step 1 — Create Custom Sensitive Information Type

Created a new Sensitive Information Type named:

**Patient Medical Record Number (MRN)**

Purpose:

Detect proprietary Medical Record Numbers used internally by Contoso Regional Medical Center.

---

## Screenshot

```
screenshots/01-create-custom-sensitive-info-type.png
```

---

# ⚙ Step 2 — Configure Detection Pattern

The detection pattern consists of:

### Primary Element

Regular Expression

```
MRN-[0-9]{8}
```

Example

```
MRN-01200951
MRN-24167737
MRN-92279447
```

---

### Supporting Keywords

Healthcare contextual keywords including:

- Medical Record No.
- Patient Name
- ICD-10 Code
- CPT Code
- Attending Physician
- Date of Birth
- Hospital Code
- Department

These contextual keywords reduce false positives by ensuring the MRN appears within a medical context.

---

### Detection Logic

High Confidence Detection

```
MRN Pattern
        +
Healthcare Keywords
        +
300 Character Proximity
```

---

## Screenshots

```
screenshots/02-configure-keyword-list.png

screenshots/03-configure-custom-regex-pattern.png

screenshots/04-review-custom-sit-pattern.png
```

---

# 🧪 Step 3 — Test Custom SIT

A test document containing multiple proprietary Medical Record Numbers was uploaded for validation.

Microsoft Purview successfully detected:

- Low Confidence
- Medium Confidence
- High Confidence

Matches for every MRN.

---

## Screenshot

```
screenshots/05-test-custom-sensitive-info-type.png
```

---

# 🔐 Step 4 — Create Data Loss Prevention Policy

Created a Custom DLP Policy.

Policy Name

```
Protect Hospital Medical Records
```

Protected Locations

- Exchange Online
- OneDrive
- Microsoft Teams
- SharePoint Online

Configured Rule

If document contains:

Patient Medical Record Number (MRN)

Actions:

- Block Access
- Block Sharing
- Generate Alert

---

## Screenshots

```
screenshots/06-create-dlp-policy-rule.png

screenshots/07-review-dlp-policy-settings.png
```

---

# 🚀 Step 5 — Deploy Policy

After policy creation:

- Synchronization completed successfully
- Policy enabled
- Protection applied across Microsoft 365 workloads

---

## Screenshot

```
screenshots/08-policy-sync-completed.png
```

---

# ✅ Step 6 — Validation

## OneDrive Upload

Uploaded patient registry document to OneDrive.

Custom SIT successfully detected proprietary MRNs.

---

```
screenshots/09-upload-document-to-onedrive.png
```

---

## Exchange Online

Attempted to email the document.

Microsoft Purview blocked transmission.

Administrator alert generated.

---

```
screenshots/10-send-document-via-email.png

screenshots/11-email-blocked-by-dlp-policy.png

screenshots/12-email-dlp-alert.png
```

---

## Microsoft Teams

Shared the same document through Teams.

Policy inspection detected the sensitive information.

Sharing was blocked according to organizational policy.

Administrator alert generated.

---

```
screenshots/13-share-document-via-teams.png

screenshots/14-teams-recipient-view.png

screenshots/15-teams-sharing-blocked-by-policy.png

screenshots/16-teams-dlp-alert.png
```

---

# 🔎 Alert Investigation

## Activity Explorer

Validated:

- DLP Rule Matched
- Policy Name
- Sensitive Information Type
- User
- File
- Action

---

```
screenshots/17-activity-explorer-rule-match.png
```

---

## Content Explorer

Verified Microsoft Purview classified the uploaded document and identified multiple Patient Medical Record Numbers (MRNs).

---

```
screenshots/18-content-explorer-detected-sensitive-data.png
```

---

## OneDrive Alert Investigation

Reviewed the generated DLP incident.

Verified:

- Policy matched
- Rule matched
- Sensitive Information Type
- User
- Alert details

---

```
screenshots/19-onedrive-dlp-alert-investigation.png
```

---

# ✅ Results

Successfully implemented:

✔ Custom Sensitive Information Type

✔ Custom Regex Detection

✔ Contextual Keyword Matching

✔ DLP Policy

✔ OneDrive Protection

✔ Exchange Protection

✔ Microsoft Teams Protection

✔ Alert Generation

✔ Activity Explorer Investigation

✔ Content Explorer Validation

---

# 🎯 Skills Demonstrated

- Microsoft Purview
- Microsoft Purview DLP
- Custom Sensitive Information Types
- Regular Expressions
- Contextual Keyword Detection
- Information Protection
- Data Classification
- Policy Creation
- Policy Deployment
- Alert Investigation
- Activity Explorer
- Content Explorer
- Exchange Online DLP
- Teams DLP
- OneDrive DLP
- Security Validation
- Incident Analysis

---

# 📚 Learning Outcomes

Through this lab I gained practical experience in:

- Designing custom enterprise data classifiers
- Building proprietary data detection patterns
- Implementing Microsoft Purview DLP policies
- Protecting sensitive healthcare information
- Validating detections across Microsoft 365 workloads
- Investigating DLP incidents using Microsoft Purview security tools
- Understanding enterprise data protection architecture

---

## 👨‍💻 Author

**Dinesh Kumar**

Cybersecurity | Microsoft Purview | Data Loss Prevention | Information Protection

GitHub: **https://github.com/DINESHKUMARDK02**
