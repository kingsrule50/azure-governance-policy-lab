<div align="center">

# 🛡️ Azure Governance with Azure Policy Lab

![AzurePolicy](https://img.shields.io/badge/Azure_Policy-Governance-0078D4?style=for-the-badge&logo=microsoftazure&logoColor=white)
![EntraID](https://img.shields.io/badge/Microsoft_Entra_ID-RBAC-0078D4?style=for-the-badge&logo=microsoftazure&logoColor=white)
![Compliance](https://img.shields.io/badge/Compliance_Dashboard-Monitored-107C10?style=for-the-badge&logo=microsoftazure&logoColor=white)
![CustomPolicy](https://img.shields.io/badge/Custom_Policy-JSON_Authored-5C2D91?style=for-the-badge&logo=microsoftazure&logoColor=white)
![ResourceLock](https://img.shields.io/badge/Resource_Lock-Delete_Protected-FF6600?style=for-the-badge&logo=microsoftazure&logoColor=white)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)

**I built an end-to-end Azure governance framework for Contoso Ltd using Azure Policy — covering built-in policy assignment, custom JSON policy authoring, policy initiative bundling, live compliance monitoring, least-privilege RBAC, and resource lock enforcement across a production-scoped resource group.**

[Overview](#-overview) • [Architecture](#-architecture) • [Environment](#-lab-environment) • [Phase Walkthrough](#-phase-walkthrough) • [Skills](#-skills-demonstrated) • [Career Relevance](#-career-relevance)

</div>

---

## 📋 Lab Summary

| Field | Detail |
|---|---|
| **Certification Alignment** | AZ-104 · AZ-500 · SC-100 · SC-300 |
| **Estimated Time** | 3–4 hours |
| **Estimated Cost** | $0 — Microsoft Azure Free Tier / Developer Tenant |
| **Difficulty** | Intermediate |
| **Platform** | Microsoft Azure Portal · Microsoft Entra ID |
| **Career Relevance** | Cloud Security Engineer · Azure Administrator · Governance Analyst · Identity Engineer |

---

## 🎯 Overview

This lab demonstrates enterprise-grade cloud governance using Azure Policy — the native Azure control plane used by organisations to enforce compliance at scale across hundreds of subscriptions and thousands of resources.

The implementation covered the full governance lifecycle for **Contoso Ltd's production environment**:

- **Built-in policy assignment** — enforcing approved regions and mandatory tagging with Deny effects
- **Custom policy authoring** — writing a JSON policy definition from scratch to block Premium storage SKUs
- **Policy initiative creation** — bundling all three policies into a single assignable governance baseline
- **Live compliance monitoring** — capturing non-compliant resources on the Azure Policy compliance dashboard
- **Automated policy denial** — demonstrating real-time resource blocking at deployment time
- **Least-privilege RBAC** — assigning the Resource Policy Contributor role at resource group scope
- **Resource lock enforcement** — protecting the production resource group from accidental deletion

> Azure Policy is the foundation of enterprise cloud governance in Microsoft environments. Hands-on experience authoring, assigning, and monitoring policies is directly applicable to cloud security, compliance, and Azure administration roles.

---

## 🏗 Architecture

### Contoso Ltd — Governance Framework

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                          AZURE SUBSCRIPTION (KINGSRULE50)                   │
│                                                                             │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │               RESOURCE GROUP: rg-contoso-governance-prod              │  │
│  │                                                                       │  │
│  │  Tags: Environment=Production | Project=Contoso-Governance            │  │
│  │        Owner=IT-Governance-Team | CostCenter=CC-1001                  │  │
│  │                                                                       │  │
│  │  ┌─────────────────┐    ┌──────────────────┐    ┌──────────────────┐  │  │
│  │  │  Storage Account │    │  Resource Lock   │    │   RBAC Role      │  │  │
│  │  │  stcontosononc.. │    │  contoso-delete- │    │  Policy Auditor  │  │  │
│  │  │  West Europe ❌  │    │  lock (Delete)   │    │  Contoso         │  │  │
│  │  │  No tags ❌      │    │  🔒 Protected    │    │  Resource Policy │  │  │
│  │  └─────────────────┘    └──────────────────┘    │  Contributor     │  │  │
│  │                                                  └──────────────────┘  │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
│                                                                             │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │                        AZURE POLICY ENGINE                            │  │
│  │                                                                       │  │
│  │  ┌─────────────────────────────────────────────────────────────────┐  │  │
│  │  │          INITIATIVE: contoso-governance-baseline                │  │  │
│  │  │                                                                 │  │  │
│  │  │  ┌─────────────────────┐  ┌─────────────────────┐              │  │  │
│  │  │  │  Allowed locations  │  │  Require a tag on   │              │  │  │
│  │  │  │  Effect: Deny       │  │  resources          │              │  │  │
│  │  │  │  Param: East US     │  │  Effect: Deny       │              │  │  │
│  │  │  │  ❌ Non-compliant   │  │  Param: Environment │              │  │  │
│  │  │  └─────────────────────┘  │  ❌ Non-compliant   │              │  │  │
│  │  │                           └─────────────────────┘              │  │  │
│  │  │  ┌─────────────────────────────────────────────────────────┐   │  │  │
│  │  │  │  contoso-restrict-premium-storage (Custom JSON Policy)  │   │  │  │
│  │  │  │  Effect: Deny | Blocks Premium_LRS and Premium_ZRS      │   │  │  │
│  │  │  │  ✅ Compliant — no Premium SKUs deployed                │   │  │  │
│  │  │  └─────────────────────────────────────────────────────────┘   │  │  │
│  │  └─────────────────────────────────────────────────────────────────┘  │  │
│  │                                                                       │  │
│  │  Compliance Dashboard: 0% | 2 Non-compliant resources                │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 🖥 Lab Environment

| Component | Detail |
|---|---|
| **Azure Subscription** | Microsoft Azure Developer Tenant (KINGSRULE50) |
| **Resource Group** | rg-contoso-governance-prod — East US |
| **Test Resource** | stcontosononcomp001 — Storage Account (West Europe, no tags) |
| **Policy Engine** | Azure Policy — Assignments, Definitions, Compliance, Remediation |
| **Identity Platform** | Microsoft Entra ID — user creation and RBAC |
| **Governance Scope** | Resource group level (rg-contoso-governance-prod) |
| **Policy Category** | Contoso-Governance (custom category) |
| **Cost** | $0 — Azure Free Tier |

### Enterprise Naming Convention

| Resource | Name |
|---|---|
| Resource Group | `rg-contoso-governance-prod` |
| Test Storage Account | `stcontosononcomp001` |
| Location Policy | `contoso-deny-unapproved-locations` |
| Tag Policy | `contoso-require-environment-tag` |
| Custom Policy | `contoso-restrict-premium-storage` |
| Initiative | `contoso-governance-baseline` |
| Initiative Assignment | `contoso-apply-governance-baseline` |
| RBAC User | `Policy Auditor Contoso` |
| Resource Lock | `contoso-delete-lock` |

---

## 🛠️ Phase Walkthrough

### Phase 1 — Environment Setup

The foundation of the governance lab was established by creating a production-scoped resource group with full enterprise tagging, deploying a deliberately non-compliant storage account in the wrong region with no tags, and exploring the Azure Policy blade before any assignments were made.

![Resource Group Overview](screenshots/01-rg-contoso-governance-prod.png)

The resource group was tagged with all four Contoso enterprise tags — Environment, Project, Owner, and CostCenter — establishing the tagging baseline the policies would enforce on all child resources.

![Non-Compliant Storage Account](screenshots/02-noncompliant-storage-account.png)

The test storage account was intentionally placed in West Europe with no tags. This resource exists specifically to generate policy violations on the compliance dashboard — demonstrating real non-compliance rather than a fabricated result.

![Azure Policy Overview](screenshots/03-azure-policy-overview.png)

---

### Phase 2 — Assign Built-in Policies

Two enterprise built-in policies were assigned to the Contoso production resource group with custom non-compliance messages, enforcing Deny effects at deployment time.

#### Policy 1 — Allowed Locations (contoso-deny-unapproved-locations)

![Location Policy Basics](screenshots/04-policy-assign-basics.png)

The policy was scoped to the resource group with East US as the only permitted deployment region. Any resource targeting a different region is denied before deployment completes.

![Location Policy Parameters](screenshots/05-policy-assign-parameters.png)

![Location Policy Non-Compliance Message](screenshots/06-policy-assign-noncompliancemsg.png)

#### Policy 2 — Require Environment Tag (contoso-require-environment-tag)

![Tag Policy Basics](screenshots/07-tag-policy-basics.png)

![Tag Policy Parameters](screenshots/08-tag-policy-parameters.png)

#### Policy Denial in Action

With both policies assigned, an attempt was made to deploy a storage account in West Europe with no tags. The Azure Portal blocked the deployment inline — displaying the custom Contoso non-compliance messages directly in the resource creation form before any deployment was submitted.

![Tag Policy Denial — Live](screenshots/09-policy-deny-tag-violation.png)

The Tags tab showed the exact Contoso non-compliance message: *"This resource is missing the required Environment tag. All Contoso resources must be tagged."*

![Policy Details Panel](screenshots/10-policy-details-tag.png)

![Location Policy Denial](screenshots/11-policy-deny-location-violation.png)

![Policy Overview Non-Compliant](screenshots/12-policy-overview-noncompliant.png)

![Policy Assignments List](screenshots/13-policy-assignments-list.png)

---

### Phase 3 — Custom Policy (JSON Authored)

A custom Azure Policy definition was written from scratch in JSON to enforce Contoso's storage cost governance standard — blocking creation of Premium SKU storage accounts across the production environment.

![Custom Policy Definition Form](screenshots/14-custom-policy-definition-form.png)

The policy was authored in the Azure Portal policy rule editor and saved under the custom **Contoso-Governance** category — clearly distinguishing it from built-in Microsoft policies.

![Custom Policy Saved Definition](screenshots/15-custom-policy-saved-definition.png)

The saved definition confirms: **Type: Custom** — this is a Contoso-authored policy, not a Microsoft built-in. The JSON policy rule blocks `Premium_LRS` and `Premium_ZRS` storage SKUs with a Deny effect.

```json
{
  "mode": "All",
  "policyRule": {
    "if": {
      "allOf": [
        {
          "field": "type",
          "equals": "Microsoft.Storage/storageAccounts"
        },
        {
          "field": "Microsoft.Storage/storageAccounts/sku.name",
          "in": [
            "Premium_LRS",
            "Premium_ZRS"
          ]
        }
      ]
    },
    "then": {
      "effect": "Deny"
    }
  },
  "parameters": {}
}
```

![Custom Policy Assignment Basics](screenshots/16-custom-policy-assign-basics.png)

![Custom Policy Assignment Review](screenshots/17-custom-policy-assign-review.png)

---

### Phase 4 — Policy Initiative (Governance Baseline)

All three policies were bundled into a single Policy Initiative — the enterprise pattern for managing multiple related governance controls as one assignable object. This mirrors how real organisations deploy governance at scale across multiple subscriptions.

![Initiative Basics](screenshots/18-initiative-basics.png)

![Initiative Policies Added](screenshots/19-initiative-policies-added.png)

Three policies added to the Contoso Governance Baseline initiative: `Allowed locations`, `Require a tag on resources`, and `contoso-restrict-premium-storage`.

![Initiative Policy Parameters](screenshots/20-initiative-policy-parameters.png)

Parameters were set inline within the initiative — East US for the location policy and Environment for the tag policy — so the initiative can be assigned as a single object without additional parameter configuration.

![Initiative Review Create](screenshots/21-initiative-review-create.png)

The Review + Create page confirms: **contoso-governance-baseline with 3 policies**.

![Initiative Saved](screenshots/22-initiative-saved.png)

![Initiative Assignment Basics](screenshots/23-initiative-assign-basics.png)

![Initiative Assignment Review](screenshots/24-initiative-assign-review.png)

---

### Phase 5 — Compliance Dashboard

With all policies and the initiative assigned, the Azure Policy compliance dashboard immediately reflected the governance state of the Contoso production environment.

![Compliance Dashboard Overview](screenshots/25-compliance-dashboard-overview.png)

**0% overall compliance** — 2 non-compliant resources detected. The dashboard shows all Contoso policy assignments with their compliance state. `contoso-restrict-premium-storage` shows **100% Compliant** because no Premium SKUs exist — demonstrating that a passing policy result is equally important evidence as a failing one.

![Initiative Compliance Policies](screenshots/26-initiative-compliance-policies.png)

Drilling into the `contoso-apply-governance-baseline` initiative shows the per-policy breakdown — Allowed locations Non-compliant, Require a tag Non-compliant, contoso-restrict-premium-storage Compliant.

![Compliance Non-Compliant Resources](screenshots/27-compliance-noncompliant-resources.png)

The Non-compliant resources tab explicitly names `stcontosononcomp001` in West Europe as the violating resource — closing the loop between the intentionally deployed non-compliant storage account and the governance engine catching it.

![Compliance Tag Policy Detail](screenshots/28-compliance-tag-policy-detail.png)

---

### Phase 6 — RBAC & Resource Locks

The final phase demonstrated enterprise identity governance — assigning the least-privilege role to a governance auditor user and protecting the production resource group from accidental deletion.

#### Microsoft Entra ID User Creation

![Entra User Basics](screenshots/30-entra-user-basics.png)

![Entra User Properties](screenshots/31-entra-user-properties.png)

A dedicated `Policy Auditor Contoso` user was created in Microsoft Entra ID with the job title IT Governance Auditor and department IT-Governance — following the Contoso enterprise identity standard.

#### RBAC Role Assignment

![RBAC Role Selection](screenshots/32-rbac-role-selection.png)

![RBAC Member Assigned](screenshots/33-rbac-member-assigned.png)

The **Resource Policy Contributor** role was selected — this is the least-privilege role that allows managing Azure Policy assignments without granting Owner or Contributor access to the resources themselves. This is the correct enterprise pattern.

![RBAC Review Assign](screenshots/34-rbac-review-assign.png)

![RBAC Role Assignments List](screenshots/35-rbac-role-assignments-list.png)

The Role Assignments tab confirms the governance access model: **Owner** (Asusu Kingsley — admin) and **Resource Policy Contributor** (Policy Auditor Contoso — least privilege). Two roles, two users, clear separation of duties.

#### Resource Lock

![Resource Lock Creation](screenshots/36-resource-lock-creation.png)

![Resource Lock Applied](screenshots/37-resource-lock-applied.png)

A **Delete** lock named `contoso-delete-lock` was applied at the resource group level — protecting all resources within from accidental deletion regardless of the user's RBAC role.

![Resource Lock Delete Blocked](screenshots/38-resource-lock-delete-blocked.png)

---

## 🧠 Skills Demonstrated

| Skill | Real-World Application |
|---|---|
| **Azure Policy assignment** | Built-in policy assignment is the primary governance mechanism used by cloud admins to enforce standards at scale |
| **Custom JSON policy authoring** | Writing policy rules from scratch demonstrates understanding of the Azure Policy schema — required for scenarios not covered by built-in policies |
| **Policy initiative management** | Initiatives are the enterprise pattern for bundling related controls — used in regulatory compliance frameworks like CIS, NIST, and ISO 27001 |
| **Compliance dashboard monitoring** | The compliance dashboard is the primary tool for auditing governance state — used in security reviews, audits, and executive reporting |
| **Policy denial testing** | Demonstrating live policy enforcement proves the governance controls are functional — not just configured |
| **Least-privilege RBAC** | Assigning the Resource Policy Contributor role at resource group scope follows Zero Trust and separation of duties principles |
| **Resource lock enforcement** | Delete locks are a critical production protection mechanism — prevents accidental or malicious resource group deletion |
| **Enterprise tagging strategy** | Consistent tagging (Environment, Project, Owner, CostCenter) enables cost attribution, resource organisation, and policy targeting |

---

## 🎯 Career Relevance

| Role | How This Lab Applies |
|---|---|
| **Cloud Security Engineer** | Azure Policy is the primary governance enforcement tool in Microsoft cloud security architectures |
| **Azure Administrator (AZ-104)** | Policy assignment, compliance monitoring, RBAC, and resource locks are core AZ-104 exam domains |
| **Azure Security Engineer (AZ-500)** | Custom policy authoring, initiative management, and governance at scale are AZ-500 competencies |
| **Governance & Compliance Analyst** | The compliance dashboard and CSV export workflows are used in audit preparation and regulatory reporting |
| **Identity Engineer** | RBAC role assignment at resource group scope and Entra ID user management are core identity engineering tasks |

---

## 🔐 Security Controls Implemented

| Control | Implementation | Outcome |
|---|---|---|
| **Regional restriction** | Allowed locations policy — Deny effect, East US only | Resources outside approved region cannot be deployed |
| **Mandatory tagging** | Require Environment tag — Deny effect | Untagged resources cannot be created — enables cost attribution |
| **SKU cost control** | Custom policy — blocks Premium_LRS and Premium_ZRS | Prevents unauthorised Premium storage spend |
| **Initiative governance** | All 3 controls bundled as contoso-governance-baseline | Single assignment delivers full governance baseline |
| **Compliance monitoring** | Azure Policy compliance dashboard | Continuous visibility into governance state across all resources |
| **Least-privilege access** | Resource Policy Contributor role at RG scope | Policy auditor cannot modify resources — only policies |
| **Deletion protection** | Delete lock on rg-contoso-governance-prod | Production resource group protected from accidental removal |

---

## 📁 Repository Structure

```
azure-governance-policy-lab/
│
├── README.md
├── policies/
│   └── contoso-restrict-premium-storage.json
├── initiatives/
│   └── contoso-governance-baseline.json
├── screenshots/
│   ├── 01-rg-contoso-governance-prod.png
│   ├── 02-noncompliant-storage-account.png
│   ├── 03-azure-policy-overview.png
│   ├── 04-policy-assign-basics.png
│   ├── 05-policy-assign-parameters.png
│   ├── 06-policy-assign-noncompliancemsg.png
│   ├── 07-tag-policy-basics.png
│   ├── 08-tag-policy-parameters.png
│   ├── 09-policy-deny-tag-violation.png
│   ├── 10-policy-details-tag.png
│   ├── 11-policy-deny-location-violation.png
│   ├── 12-policy-overview-noncompliant.png
│   ├── 13-policy-assignments-list.png
│   ├── 14-custom-policy-definition-form.png
│   ├── 15-custom-policy-saved-definition.png
│   ├── 16-custom-policy-assign-basics.png
│   ├── 17-custom-policy-assign-review.png
│   ├── 18-initiative-basics.png
│   ├── 19-initiative-policies-added.png
│   ├── 20-initiative-policy-parameters.png
│   ├── 21-initiative-review-create.png
│   ├── 22-initiative-saved.png
│   ├── 23-initiative-assign-basics.png
│   ├── 24-initiative-assign-review.png
│   ├── 25-compliance-dashboard-overview.png
│   ├── 26-initiative-compliance-policies.png
│   ├── 27-compliance-noncompliant-resources.png
│   ├── 28-compliance-tag-policy-detail.png
│   ├── 29-compliance-export-csv.png
│   ├── 30-entra-user-basics.png
│   ├── 31-entra-user-properties.png
│   ├── 32-rbac-role-selection.png
│   ├── 33-rbac-member-assigned.png
│   ├── 34-rbac-review-assign.png
│   ├── 35-rbac-role-assignments-list.png
│   ├── 36-resource-lock-creation.png
│   ├── 37-resource-lock-applied.png
│   └── 38-resource-lock-delete-blocked.png
└── docs/
    └── compliance-export.csv
```

---

## 🔗 Related Labs

| Lab | Description |
|---|---|
| **[Windows Autopilot & Intune Lab](https://github.com/kingsrule50/windows-autopilot-intune)** | Zero-touch Windows deployment pipeline using Microsoft Intune and Entra ID |
| **[Conditional Access & MFA Lab](https://github.com/kingsrule50/conditional-access-mfa-lab)** | Layered Conditional Access architecture — MFA enforcement and Tor-based risk detection |
| **[Privileged Identity Management Lab](https://github.com/kingsrule50/privileged-identity-management-pim-lab)** | Just-In-Time privileged access control using Microsoft Entra PIM |
| **[Azure SOC Homelab](https://github.com/kingsrule50/azure-soc-homelab)** | Splunk SIEM on Azure — AD log ingestion, SPL detection rules, and automated alerting |

---

## 📚 References

- [Azure Policy Documentation — Microsoft](https://learn.microsoft.com/en-us/azure/governance/policy/)
- [Azure Policy Definition Structure](https://learn.microsoft.com/en-us/azure/governance/policy/concepts/definition-structure)
- [Azure Policy Initiative Definition Structure](https://learn.microsoft.com/en-us/azure/governance/policy/concepts/initiative-definition-structure)
- [Azure RBAC Built-in Roles](https://learn.microsoft.com/en-us/azure/role-based-access-control/built-in-roles)
- [Azure Resource Locks](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/lock-resources)
- [AZ-104 Azure Administrator Certification](https://learn.microsoft.com/en-us/credentials/certifications/azure-administrator/)
- [AZ-500 Azure Security Engineer Certification](https://learn.microsoft.com/en-us/credentials/certifications/azure-security-engineer/)

---

<div align="center">

**Chinedu Kingsley Asuzu**
Cloud Security Engineer · Microsoft Azure · Governance & Identity

*Part of a hands-on cloud security lab series · Microsoft Azure Developer Tenant · $0 cost*

</div>

