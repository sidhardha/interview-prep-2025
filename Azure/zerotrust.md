# Implementing Zero-Trust Security in Azure: Step-by-Step Example

This example demonstrates how a financial services company can implement Zero-Trust Security in Azure, with configuration guidance for each component.

---

## 1. Enforce Conditional Access and MFA via Azure AD

### a. Enable Security Defaults (MFA for all users)
- In Azure Portal, go to **Azure Active Directory > Properties > Manage Security defaults**.
- Set to **Enable**.

### b. Create a Conditional Access Policy
- Go to **Azure AD > Security > Conditional Access > New policy**.
- Name: “Require MFA for All Users”
- Assignments: Users/Groups = All users (exclude break-glass accounts).
- Cloud apps: All cloud apps.
- Conditions: Locations = Any location (or exclude trusted locations).
- Access controls: Grant = Require multi-factor authentication.
- Enable policy.

### c. Disable Legacy Authentication
- Go to **Azure AD > Security > Conditional Access**.
- Create a policy to block legacy authentication clients.

---

## 2. Use NSGs, Azure Firewall, and Private Endpoints

### a. Network Security Groups (NSGs)
- Create NSGs for each subnet.
- Allow only required inbound/outbound traffic (e.g., allow HTTPS, block RDP/SSH from internet).
- Associate NSGs with subnets or NICs.

### b. Azure Firewall
- Deploy Azure Firewall in a dedicated subnet (e.g., “AzureFirewallSubnet”).
- Route all outbound traffic through the firewall using UDRs (User Defined Routes).
- Configure application and network rules to allow only necessary traffic.

### c. Private Endpoints
- For PaaS services (e.g., Azure SQL, Storage), create Private Endpoints in your VNet.
- Disable public network access on the PaaS resource.
- Update DNS to resolve service names to private IPs.

---

## 3. Apply Least Privilege with RBAC and PIM

### a. Role-Based Access Control (RBAC)
- Assign users/groups only the minimum required roles (e.g., Reader, Contributor).
- Use built-in roles or create custom roles for granular permissions.
- Assign roles at the lowest possible scope (resource/resource group).

### b. Privileged Identity Management (PIM)
- Go to **Azure AD > Privileged Identity Management**.
- Enable PIM for Azure resources and Azure AD roles.
- Require approval and MFA for role activation.
- Set activation time limits (e.g., 1 hour for Owner role).
- Review and audit privileged role assignments regularly.

---

## 4. Monitor with Azure Sentinel and Defender for Cloud

### a. Azure Sentinel
- Deploy Azure Sentinel in your Log Analytics workspace.
- Connect data sources: Azure AD, Azure Activity, Security events, etc.
- Create analytics rules for threat detection (e.g., impossible travel, brute force).
- Set up workbooks and dashboards for monitoring.

### b. Microsoft Defender for Cloud
- Enable Defender for Cloud on all subscriptions.
- Review Secure Score and recommendations.
- Enable Just-in-Time (JIT) VM access to limit RDP/SSH exposure.
- Enable advanced threat protection for PaaS services.

---

## 5. Example Scenario: Financial Services Company

- **Conditional Access:** All users must use MFA; legacy authentication is blocked.
- **Network Security:** All VMs are in subnets protected by NSGs and Azure Firewall; only HTTPS is allowed from the internet.
- **Private Endpoints:** Azure SQL and Storage accounts are only accessible via private endpoints.
- **RBAC & PIM:** Admins must activate privileged roles via PIM with approval and MFA; no standing admin access.
- **Monitoring:** Azure Sentinel and Defender for Cloud provide real-time threat detection and compliance monitoring.
- **JIT Access:** Admins request JIT access for VMs, which is time-limited and audited.

---

## References
- [Zero Trust Guidance](https://learn.microsoft.com/en-us/security/zero-trust/)
- [Conditional Access](https://learn.microsoft.com/en-us/azure/active-directory/conditional-access/)
- [Azure Firewall](https://learn.microsoft.com/en-us/azure/firewall/)
- [Azure PIM](https://learn.microsoft.com/en-us/azure/active-directory/privileged-identity-management/)
- [Azure Sentinel](https://learn.microsoft.com/en-us/azure/sentinel/)

*Let me know if you want sample scripts, ARM/Bicep templates, or a Markdown diagram for this scenario!*