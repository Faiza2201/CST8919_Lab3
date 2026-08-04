# CST8919_Lab3

**Student Name**: Faiza Boudehane
**Student ID**: 041273470
**Semester**: Spring 2026

---

## Demo Video

🎥 [Watch Demo Video](https://youtu.be/xSUOI_3lnnM)


# MapleTech Secure Foundation – Azure Policy Lab

## Summary

MapleTech Solutions, a rapidly growing Canadian cloud-native company, needed to bring
order to its Azure environment. Developers were deploying resources across multiple
regions, exposing public IP addresses, and skipping resource tagging — creating
compliance, cost-tracking, and security risks.

This lab implements governance guardrails using **Azure Policy** to enforce three
rules across a scoped resource group:

1. Resources must be deployed only in **Canada Central**.
2. All resources must carry a **ProjectName** tag.
3. **Public IP addresses** cannot be created.

The three custom policies were grouped into a single **Policy Initiative**
(`MapleTech Secure Foundation`) and assigned to the `rg-mapletech-dev` resource
group with enforcement mode set to **Default** (i.e., Deny effects are actively
enforced, not just audited). The initiative was then tested against four
deployment scenarios to confirm both denial and successful compliant deployment.

## Explanation of Each Policy

### Only-CanadaCentral
- **Effect:** Deny
- **Logic:** Checks the `location` field of any resource. If it does not equal
  `canadacentral`, the deployment is denied.
- **Purpose:** Enforces data residency and keeps all company resources
  consolidated in a single region for consistency, latency, and cost predictability.

### Require-ProjectName-Tag
- **Effect:** Deny
- **Logic:** Checks whether the `ProjectName` tag exists on a resource. If the
  tag is missing, the deployment is denied.
- **Purpose:** Ensures every resource can be traced back to a project for cost
  allocation, ownership accountability, and cleanup during audits.

### Deny-Public-IP
- **Effect:** Deny
- **Logic:** Checks if the resource type is `Microsoft.Network/publicIPAddresses`.
  If so, the deployment is denied outright.
- **Purpose:** Reduces the organization's public attack surface by preventing
  any resource — including VMs — from being assigned a public-facing IP address.

### Initiative: MapleTech Secure Foundation
All three policies above were grouped into a single initiative so they can be
assigned, versioned, and audited together as one governance package, rather
than managing three separate assignments.

## Test Results

| Test Case | Expected | Result |
|---|---|---|
| Deploy VM in East US | ❌ Denied | ❌ Denied — blocked by `Only-CanadaCentral` |
| Deploy Storage Account without ProjectName tag | ❌ Denied | ❌ Denied — blocked by `Require-ProjectName-Tag` |
| Create a Public IP (standalone, and by default during VM creation) | ❌ Denied | ❌ Denied — blocked by `Deny-Public-IP` |
| Deploy VM in Canada Central, tagged, Public IP set to "None" | ✅ Allowed | ✅ Allowed |
