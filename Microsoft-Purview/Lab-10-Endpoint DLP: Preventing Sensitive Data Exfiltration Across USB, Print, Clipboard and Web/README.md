# Lab 10 – Endpoint DLP: Preventing Sensitive Data Exfiltration Across USB, Print, Clipboard and Web

![Microsoft Purview](https://img.shields.io/badge/Microsoft%20Purview-Endpoint%20DLP-0078D4?style=for-the-badge&logo=microsoft)
![Windows 11](https://img.shields.io/badge/Windows%2011-Managed%20Endpoint-0078D4?style=for-the-badge&logo=windows)
![DLP](https://img.shields.io/badge/Data%20Loss%20Prevention-Endpoint%20Security-8B1A1A?style=for-the-badge)
![Status](https://img.shields.io/badge/Lab-Completed-success?style=for-the-badge)

> Implemented and validated Microsoft Purview Endpoint DLP controls to prevent synthetic Contoso customer data from leaving a managed Windows endpoint through USB media, printing, clipboard operations, and an unauthorized cloud service.

## Table of Contents

- [Overview](#overview)
- [Business Scenario](#business-scenario)
- [Objectives](#objectives)
- [Architecture](#architecture)
- [Detection and Enforcement Design](#detection-and-enforcement-design)
- [Policy Configuration](#policy-configuration)
- [Validation Tests](#validation-tests)
- [Investigation and Alerting](#investigation-and-alerting)
- [Results](#results)
- [Key Takeaways](#key-takeaways)
- [Technologies Used](#technologies-used)
- [Disclaimer](#disclaimer)

## Overview

This lab demonstrates an end-to-end Microsoft Purview Endpoint DLP implementation on an already onboarded Windows 11 endpoint.

The policy detects the custom **Contoso Customer ID** Sensitive Information Type (SIT) and restricts high-risk endpoint activities when sensitive customer data is involved.

The following data-exfiltration channels were tested:

- Copy to removable USB media
- Print
- Copy to clipboard
- Upload to Google Drive, configured as an unauthorized cloud destination

The lab also includes negative-control testing with a public document to confirm that the policy applies to sensitive content rather than indiscriminately blocking USB or cloud services.

## Business Scenario

Contoso employees need normal access to customer information on managed corporate devices. However, customer records must not be transferred through unapproved channels such as removable storage, printing, clipboard operations, or personal cloud storage.

The security requirement is:

> Allow legitimate local use of business documents while preventing sensitive customer data from leaving managed endpoints through high-risk data-transfer activities.

Two synthetic files were used:

| File | Purpose | Expected result |
|---|---|---|
| `Contoso-Sensitive-Customer-Records.docx` | Contains multiple `CUS#####` customer IDs | Restricted |
| `Contoso-Public-Product-Information.docx` | Contains no customer identifiers | Allowed |

## Objectives

1. Configure an unauthorized cloud-service domain group for Google Drive.
2. Create an Endpoint DLP policy using the custom **Contoso Customer ID** SIT.
3. Restrict USB, print, clipboard, and cloud-upload activities.
4. Configure end-user notifications and administrator alerts.
5. Validate sensitive-file enforcement.
6. Validate public-file access as a negative control.
7. Investigate endpoint events in Activity Explorer.
8. Review the resulting DLP alerts.

## Architecture

```mermaid
flowchart TD
    A["Managed Windows 11 Endpoint"] --> B["Sensitive Customer Document"]
    B --> C["Microsoft Purview Endpoint DLP"]
    C --> D{"Contoso Customer ID SIT detected?"}

    D -->|"No"| E["Allow normal activity"]
    D -->|"Yes"| F["Apply Endpoint DLP policy"]

    F --> G["USB Copy: Block"]
    F --> H["Print: Block"]
    F --> I["Clipboard Copy: Block"]
    F --> J["Google Drive Upload: Block"]

    G --> K["User Notification"]
    H --> K
    I --> K
    J --> K

    K --> L["Activity Explorer"]
    L --> M["DLP Alert Investigation"]
```

## Detection and Enforcement Design

### Detection condition

The Endpoint DLP rule detects the custom Sensitive Information Type:

```text
Contoso Customer ID
```

The rule uses high-confidence matching with one or more instances required.

![Custom SIT condition](screenshots/03-dlp-rule-contoso-customer-id-condition.png)

### Restricted cloud destination

Google Drive was configured as a sensitive service domain under:

```text
Contoso Unapproved Cloud Services
```

![Sensitive service domain group](screenshots/01-google-drive-sensitive-service-domain-group.png)

### Endpoint restrictions

The policy blocks the following activities for files matching the Contoso Customer ID SIT:

| Activity | Enforcement |
|---|---|
| Copy to clipboard | Block |
| Copy to removable USB device | Block |
| Copy to network share | Block |
| Print | Block |
| Upload to restricted cloud service | Block |

![Endpoint activity restrictions](screenshots/04-endpoint-dlp-file-activity-restrictions.png)

![Restricted cloud upload configuration](screenshots/06-restricted-cloud-service-upload-block.png)

## Policy Configuration

### Policy

```text
Protect Contoso Sensitive Data on Corporate Endpoints
```

![Policy name and description](screenshots/02-endpoint-dlp-policy-name-and-description.png)

### Rule

```text
Restrict Contoso Customer Data Exfiltration
```

The rule connects the sensitive-data condition with Endpoint DLP enforcement, user notification, and alerting.

![Advanced DLP rule summary](screenshots/09-advanced-dlp-rule-summary.png)

### User notification

A custom notification explains why the endpoint activity was restricted.

```text
Sensitive Contoso customer information cannot be transferred
through this activity because of your organization's data
protection policy.
```

![Custom user notification](screenshots/07-endpoint-dlp-custom-user-notification.png)

### Alerting

Incident reporting was enabled with Medium severity and alerts generated for each rule match.

![Incident alert configuration](screenshots/08-dlp-incident-alert-configuration.png)

### Deployment

The policy was configured for immediate enforcement and successfully synchronized.

![Policy enforcement mode](screenshots/10-dlp-policy-enforcement-mode.png)

![Policy final review](screenshots/11-endpoint-dlp-policy-final-review.png)

![Policy synchronization completed](screenshots/12-dlp-policy-sync-completed.png)

## Validation Tests

### USB transfer — sensitive file

The sensitive customer-record document was selected for transfer to removable USB media.

![Sensitive USB copy attempt](screenshots/15-sensitive-file-usb-copy-selection.png)

Microsoft Purview Endpoint DLP blocked the transfer and displayed the configured policy notification.

![Sensitive USB transfer blocked](screenshots/13-sensitive-file-usb-copy-blocked-notification.png)

**Result: Blocked ✅**

### USB transfer — public-file control test

The public product-information document was tested against the same USB drive.

![Public USB copy attempt](screenshots/14-public-file-usb-copy-attempt.png)

The public document successfully appeared on the removable USB device.

![Public USB copy allowed](screenshots/16-public-file-usb-copy-allowed.png)

**Result: Allowed ✅**

This demonstrates that the control is content-aware. USB devices were not universally disabled; only sensitive content was restricted.

### Print — sensitive file

A print action was attempted against the sensitive customer-record document.

![Sensitive file print attempt](screenshots/17-sensitive-file-print-attempt.png)

The endpoint notification confirmed the policy restriction.

![Sensitive file policy notification](screenshots/18-sensitive-file-policy-notification.png)

The blocked-actions dialog recorded both the USB and print restrictions for the sensitive document.

![Blocked endpoint actions](screenshots/19-usb-and-print-blocked-actions-dialog.png)

**Result: Blocked ✅**

### Google Drive upload — public-file control test

The public product-information document was selected for upload to Google Drive.

![Public file Google Drive upload selection](screenshots/20-public-file-google-drive-upload-selection..png)

The upload completed successfully.

![Public file Google Drive upload success](screenshots/21-public-file-google-drive-upload-success.png)

**Result: Allowed ✅**

### Google Drive upload — sensitive file

The sensitive customer-record document was then selected for upload to the same Google Drive destination.

![Sensitive file Google Drive upload selection](screenshots/22-sensitive-file-google-drive-upload-selection.png)

Microsoft Purview blocked the upload and displayed the custom Company Policy notification.

![Sensitive file Google Drive upload blocked](screenshots/23-sensitive-file-google-drive-upload-blocked.png)

**Result: Blocked ✅**

### Clipboard — sensitive file

A clipboard-copy action was attempted from a document containing the `CUS10002` customer identifier.

![Clipboard copy attempt](screenshots/24-cus10002-clipboard-copy-attempt.png)

The copy activity triggered the configured Endpoint DLP user notification.

![Clipboard copy blocked notification](screenshots/25-clipboard-copy-blocked-dlp-notification.png)

**Result: Blocked ✅**

## Investigation and Alerting

### Activity Explorer — cloud upload

Activity Explorer confirmed that the sensitive document was copied to the `drive.google.com` destination and enforcement mode was **Block**.

The event also shows the matched sensitive information type, policy, endpoint location, and user context.

![Activity Explorer cloud upload block](screenshots/26-activity-explorer-google-drive-upload-blocked.png)

### Activity Explorer — clipboard

Activity Explorer recorded the clipboard event with:

```text
Activity: File copied to clipboard
Enforcement mode: Block
Sensitive info type: Contoso Customer ID
Location: Endpoint devices
Application: WINWORD.EXE
```

![Activity Explorer clipboard block](screenshots/27-activity-explorer-clipboard-copy-blocked.png)

### DLP alert investigation

The DLP alert confirms that the clipboard event triggered a policy match on the endpoint device.

![Clipboard DLP alert](screenshots/28-dlp-alert-clipboard-policy-match.png)

The alert investigation also validates the policy, rule, and matched Sensitive Information Type.

![DLP alert policy and SIT details](screenshots/29-dlp-alert-policy-rule-and-sensitive-info-details.png)

## Results

| Test scenario | Sensitive file | Public-file control | Result |
|---|---:|---:|---|
| Copy to USB | Blocked | Allowed | ✅ |
| Print | Blocked | Not tested | ✅ |
| Copy to clipboard | Blocked | Not tested | ✅ |
| Upload to Google Drive | Blocked | Allowed | ✅ |
| Activity Explorer telemetry | Recorded | N/A | ✅ |
| DLP alert generation | Generated | N/A | ✅ |

## Security Engineering Analysis

This lab demonstrates more than simply disabling endpoint capabilities.

The policy uses content-aware detection to differentiate between sensitive and non-sensitive files:

```text
Public product document
        ↓
No Contoso Customer ID match
        ↓
Allowed

Sensitive customer record
        ↓
Contoso Customer ID SIT match
        ↓
Endpoint DLP enforcement
        ↓
Blocked
```

This approach reduces unnecessary disruption while protecting sensitive business information across multiple endpoint exfiltration channels.

## Key Takeaways

- Endpoint DLP extends data protection beyond email, Teams, SharePoint, and OneDrive.
- Sensitive service domain groups enable differentiated web and cloud-service controls.
- USB protection should be validated with both sensitive and non-sensitive files.
- User notifications help communicate policy decisions to employees.
- Activity Explorer provides valuable operational telemetry for endpoint events.
- DLP alerts connect endpoint enforcement with investigation workflows.
- Effective DLP controls require both prevention and visibility.

## Technologies Used

| Technology | Purpose |
|---|---|
| Microsoft Purview | Data protection and compliance platform |
| Microsoft Purview Endpoint DLP | Endpoint activity monitoring and enforcement |
| Microsoft Defender for Endpoint | Endpoint onboarding prerequisite |
| Custom Sensitive Information Type | Detects Contoso customer identifiers |
| Windows 11 Enterprise | Managed endpoint test environment |
| Microsoft Edge | Browser-based cloud-upload testing |
| Google Drive | Simulated unauthorized cloud destination |
| Activity Explorer | Endpoint event investigation |
| Purview DLP Alerts | Incident visibility and triage |

## Disclaimer

This lab was completed in a controlled Microsoft 365 demonstration environment using synthetic Contoso data. No production customer information, credentials, or confidential organizational data is included.

## Author

**Dinesh Kumar**

Microsoft Security | Data Loss Prevention | Microsoft Purview | Endpoint Security
