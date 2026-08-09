# Lab 11 — Microsoft Purview Endpoint DLP for GitHub Upload Protection

<p align="center">
  <img src="https://img.shields.io/badge/Microsoft-Purview-0078D4?logo=microsoft&logoColor=white" alt="Microsoft Purview">
  <img src="https://img.shields.io/badge/Endpoint_DLP-Enabled-107C10" alt="Endpoint DLP enabled">
  <img src="https://img.shields.io/badge/Microsoft_Edge-Validated-0078D7?logo=microsoftedge&logoColor=white" alt="Microsoft Edge validated">
  <img src="https://img.shields.io/badge/Google_Chrome-Validated-4285F4?logo=googlechrome&logoColor=white" alt="Google Chrome validated">
  <img src="https://img.shields.io/badge/Enforcement-Block-D13438" alt="Block enforcement">
</p>

> **Objective:** Configure Microsoft Purview Endpoint Data Loss Prevention (DLP) to detect source-code content and block uploads to GitHub from an onboarded Windows 11 device using **Microsoft Edge** and **Google Chrome**.

## Table of contents

- [Scenario](#scenario)
- [Architecture](#architecture)
- [Scope and design decisions](#scope-and-design-decisions)
- [Prerequisites](#prerequisites)
- [Implementation](#implementation)
  - [1. Create the sensitive service domain group](#1-create-the-sensitive-service-domain-group)
  - [2. Create the Endpoint DLP policy](#2-create-the-endpoint-dlp-policy)
  - [3. Verify policy synchronization](#3-verify-policy-synchronization)
  - [4. Deploy and validate the Chrome extension](#4-deploy-and-validate-the-chrome-extension)
- [Validation results](#validation-results)
- [Evidence summary](#evidence-summary)
- [Lessons learned](#lessons-learned)
- [References](#references)

## Scenario

Engineering source code is a high-value asset. This lab prevents a user from uploading a document containing synthetic source code to GitHub, an external public code repository. The policy uses the **Source code** trainable classifier and the **Block** action for the GitHub sensitive service domain group.

The test document contains synthetic Contoso customer API code only. It contains no production credentials, customer data, or external-service secrets.

## Architecture

```mermaid
flowchart LR
    U["User on Windows 11<br/>onboarded endpoint"] --> F["Synthetic source-code<br/>DOCX test file"]
    F --> C["Microsoft Purview Endpoint DLP"]
    C --> T["Source code<br/>trainable classifier"]
    T --> R["DLP rule:<br/>Block Source Code Uploads to GitHub"]
    R --> G["Engineering Public Code Repositories<br/>github.com · gist.github.com · raw.githubusercontent.com"]
    R --> E["Microsoft Edge<br/>native support"]
    R --> CH["Google Chrome<br/>Microsoft Purview extension"]
    E --> B["Upload blocked"]
    CH --> B
    B --> A["Purview alerts and<br/>Activity explorer evidence"]
```

## Scope and design decisions

| Area | Configuration |
|---|---|
| Protected location | **Devices** only |
| Sensitive content detection | **Source code** trainable classifier |
| Protected destination | GitHub service-domain group |
| Enforcement | **Block** |
| Validated browsers | **Microsoft Edge** and **Google Chrome** |
| Chrome prerequisite | Microsoft Purview extension installed through Intune |
| Excluded from this lab | Firefox; no Firefox validation or claims are included |

## Prerequisites

- Windows 10/11 device onboarded to Microsoft Purview.
- Microsoft Defender for Endpoint real-time protection and behavior monitoring enabled.
- Microsoft Purview DLP permissions sufficient to configure Endpoint DLP settings and policies.
- Intune available to deploy the Microsoft Purview extension to Google Chrome.
- A test document expected to match the **Source code** trainable classifier.

## Implementation

### 1. Create the sensitive service domain group

In **Microsoft Purview portal → Settings → Data Loss Prevention → Endpoint DLP settings**, create the sensitive service domain group named **Engineering Public Code Repositories**.

The group contains these GitHub destinations:

- `github.com`
- `gist.github.com`
- `raw.githubusercontent.com`

![Sensitive service domain group with GitHub domains](images/07-purview-sensitive-service-domain-group-github-domains.png)

![Saved sensitive service domain group](images/08-purview-sensitive-service-domain-group-created.png)

### 2. Create the Endpoint DLP policy

Create a custom policy named **Protect Engineering Source Code** with the description: *Prevent source code from being uploaded to external public repositories.*

![DLP policy name and description](images/09-purview-dlp-policy-name-and-description.png)

Select **Devices** as the only policy location.

![Devices selected as the DLP location](images/10-purview-dlp-policy-devices-location.png)

Create the rule **Block Source Code Uploads to GitHub**. Configure the **Source code** trainable classifier as the content condition.

![Source code classifier selected](images/11-purview-dlp-rule-source-code-classifier.png)

Configure the **Engineering Public Code Repositories** sensitive service domain group with the **Block** action, then apply **Block** to the upload-to-restricted-cloud-service-domain activity.

![GitHub service domain group set to Block](images/12-purview-dlp-github-domain-group-block-action.png)

![Upload to restricted cloud service domain set to Block](images/13-purview-dlp-upload-to-restricted-domain-block.png)

Enable a custom endpoint policy-tip notification:

> **Restricted**  
> Source code upload to GitHub site restricted by company policy.

![Custom Endpoint DLP policy tip](images/14-purview-dlp-custom-policy-tip.png)

The completed rule summary confirms the classifier, endpoint device action, and notification action.

![DLP rule summary](images/15-purview-dlp-rule-summary.png)

![Policy review before submission](images/16-purview-dlp-policy-review-before-submit.png)

### 3. Verify policy synchronization

The cloud policy synchronization completes successfully, then the individual Windows endpoint reports both configuration and DLP policy synchronization as updated.

![Cloud policy synchronization completed](images/17-purview-dlp-policy-cloud-sync-completed.png)

![Endpoint configuration and DLP policy sync updated](images/18-purview-endpoint-device-policy-sync-updated.png)

![Assigned DLP policy updated on the endpoint](images/19-purview-endpoint-assigned-dlp-policy-updated.png)

### 4. Deploy and validate the Chrome extension

An Intune Settings catalog profile named **Microsoft Purview DLP - Chrome Extension** is created to force-install the Microsoft Purview browser extension for Chrome.

![Intune Chrome extension profile basics](images/22-intune-chrome-purview-extension-profile-basics.png)

![Intune Chrome extension profile created](images/23-intune-chrome-purview-extension-profile-created.png)

The Chrome extension is present on the test device.

![Microsoft Purview extension installed in Chrome](images/04-chrome-purview-extension-installed.png)

## Validation results

### Microsoft Edge validation

The GitHub upload attempt is blocked and displays the configured Microsoft Purview notification.

![Microsoft Edge GitHub upload](images/02-edge-github-upload-file-picker.png)

![Microsoft Edge GitHub upload blocked](images/03-edge-github-upload-blocked-purview-policy.png)

Activity explorer provides the authoritative browser attribution: the application is **`msedge.exe`**, the target domain is **`github.com`**, the action is **File copied to cloud**, and enforcement mode is **Block**.

![Edge Activity explorer block details](images/26-purview-activity-explorer-edge-github-upload-block.png)

![Edge Activity explorer classifier and site-group details](images/27-purview-activity-explorer-edge-rule-classifier-details.png)

### Google Chrome validation

The Chrome upload attempt is blocked and displays the Data Loss Prevention notification.

![Google Chrome GitHub upload](images/05-chrome-github-upload-file-picker.png)

![Google Chrome GitHub upload blocked](images/06-chrome-github-upload-blocked-purview-policy.png)

Activity explorer provides the authoritative browser attribution: the application is **`chrome.exe`**, the **Source code** trainable classifier matched, the configured site group is **Engineering Public Code Repositories**, and the destination is cloud-based.

![Chrome Activity explorer classifier and site-group details](images/29-purview-activity-explorer-chrome-rule-classifier-details.png)

### Alert evidence

Microsoft Purview generated an active alert for the policy match. The alert identifies the protected document, the **Protect Engineering Source Code** policy, and the **Block Source Code Uploads to GitHub** rule.

![Purview alert for the GitHub upload policy match](images/24-purview-alert-github-upload-policy-match.png)

## Evidence summary

| Validation item | Confirmed evidence |
|---|---|
| GitHub destinations defined | Sensitive service domain group contains `github.com`, `gist.github.com`, and `raw.githubusercontent.com` |
| Content detection | **Source code** trainable classifier configured and recorded in Activity explorer |
| Enforcement | Upload-to-cloud activity shows **Block** enforcement mode |
| Endpoint readiness | Device configuration and DLP policy sync show **Updated** |
| Microsoft Edge test | Activity explorer identifies `msedge.exe` |
| Google Chrome test | Activity explorer identifies `chrome.exe` |
| Chrome extension | Microsoft Purview extension visible in Chrome |
| Audit trail | Purview alert and Activity explorer records created |

## Lessons learned

- Endpoint DLP policy cloud synchronization and endpoint synchronization are separate checks. A successful cloud sync alone does not prove that a particular device has received the policy.
- Google Chrome requires the Microsoft Purview extension for this browser-based Endpoint DLP scenario; Microsoft Edge is validated directly.
- Activity explorer is the strongest evidence for browser attribution because it records the executable name, destination domain, enforcement mode, policy, rule, and classifier.
- The policy’s scope in this lab is deliberately limited to the Edge and Chrome validation path. Firefox is not part of the documented design, deployment, or test result.

## References

- [Microsoft Learn — Help prevent sharing of sensitive items with unauthorized cloud apps and services](https://learn.microsoft.com/en-us/purview/endpoint-dlp-create-policy-unauthorized-cloud-apps-services)
- [Microsoft Learn — Configure Endpoint DLP settings](https://learn.microsoft.com/en-us/purview/dlp-configure-endpoint-settings)
- [Microsoft Learn — Learn about the Microsoft Purview extension for Chrome](https://learn.microsoft.com/en-us/purview/dlp-chrome-learn-about)
- [Microsoft Learn — Get started with the Microsoft Purview extension for Chrome](https://learn.microsoft.com/en-ca/purview/dlp-chrome-get-started)

---

<p align="center">Built as a controlled Microsoft Purview Endpoint DLP lab using synthetic test data.</p>

## Disclaimer

This lab was completed in a controlled Microsoft 365 demonstration environment using synthetic Contoso data. No production customer information, credentials, or confidential organizational data is included.

# 👨‍💻 Author

**Dinesh Kumar**

Cybersecurity Engineer | Microsoft Purview | Microsoft 365 Security | Data Loss Prevention

---

<p align="center">

**⭐ If you found this project useful, consider starring the repository.**

</p>

---
