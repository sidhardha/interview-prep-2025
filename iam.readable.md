# Azure Identity and Access Management (IAM) – Senior Engineer Guide

## 1. Azure Active Directory (Azure AD)

### Implementation and Architecture
- Azure AD is Microsoft’s cloud-based identity and access management service.
- Provides authentication, SSO, and directory services for users, devices, and applications.
- Multi-tenant, highly available, globally distributed. Integrates with on-premises AD via Azure AD Connect.

**Example:**
A company creates an Azure AD tenant to manage employee identities, enabling SSO for Microsoft 365 and custom apps.

**Reference:** [What is Azure AD?](https://learn.microsoft.com/en-us/azure/active-directory/fundamentals/active-directory-whatis)

---

### Authentication Methods and Protocols
- Supports OAuth 2.0, OpenID Connect (OIDC), SAML 2.0, WS-Fed, and legacy protocols.
- OAuth 2.0/OIDC: Used for modern web/mobile apps.
- SAML: Used for integrating with SaaS apps (e.g., Salesforce).

**Example:**
Register a web app in Azure AD, configure it to use OIDC for user authentication.

**Reference:** [Azure AD Authentication Protocols](https://learn.microsoft.com/en-us/azure/active-directory/develop/authentication-scenarios)

---

### Multi-tenant vs Single-tenant Scenarios
- Single-tenant: App is accessible only within one Azure AD tenant.
- Multi-tenant: App can be used by users from any Azure AD tenant.

**Example:**
A SaaS provider registers a multi-tenant app so customers from different organizations can sign in.

**Reference:** [Single-tenant vs Multi-tenant](https://learn.microsoft.com/en-us/azure/active-directory/develop/single-and-multi-tenant-apps)

---

### Conditional Access Policies
- Policies that enforce access controls based on user, location, device, risk, etc.
- Common use: Require MFA for users accessing from outside the corporate network.

**Example:**
Create a policy to require MFA for all users accessing the Azure portal.

**Reference:** [Conditional Access in Azure AD](https://learn.microsoft.com/en-us/azure/active-directory/conditional-access/overview)

---

### B2B and B2C Collaboration
- B2B: Invite external users to collaborate using their own credentials.
- B2C: Customer-facing identity platform for apps (supports social and local accounts).

**Example:**
Use Azure AD B2B to invite a partner’s users to access SharePoint.

**Reference:** [Azure AD B2B](https://learn.microsoft.com/en-us/azure/active-directory/external-identities/what-is-b2b) | [Azure AD B2C](https://learn.microsoft.com/en-us/azure/active-directory-b2c/overview)

---

## 2. Role-Based Access Control (RBAC)

### Built-in and Custom Roles
- Built-in: Owner, Contributor, Reader, etc.
- Custom: Define granular permissions as per business needs.

**Example:**
Create a custom role that allows only VM start/stop actions.

**Reference:** [Azure RBAC roles](https://learn.microsoft.com/en-us/azure/role-based-access-control/built-in-roles)

---

### Scope Levels
- Management Group > Subscription > Resource Group > Resource.
- Assign roles at the lowest scope needed.

**Example:**
Assign Reader role at the Resource Group level for a support team.

**Reference:** [RBAC Scope](https://learn.microsoft.com/en-us/azure/role-based-access-control/scope-overview)

---

### Role Assignments and Inheritance
- Role assignments at a higher scope are inherited by lower scopes.

**Example:**
Assign Contributor at Subscription level; user can manage all resources in all resource groups.

---

### Least Privilege Principles
- Grant only the permissions required to perform a task.

**Example:**
Use the built-in Virtual Machine Contributor role instead of Owner for VM admins.

---

### Resource Delegation
- Delegate resource management using RBAC without sharing credentials.

**Example:**
Delegate management of a storage account to a specific user.

---

## 3. Security and Compliance

### Zero Trust Architecture
- Never trust, always verify. Enforce least privilege, strong authentication, and continuous monitoring.

**Example:**
Implement Conditional Access, MFA, and device compliance policies.

**Reference:** [Zero Trust in Azure](https://learn.microsoft.com/en-us/security/zero-trust/)

---

### Privileged Identity Management (PIM)
- Manage, control, and monitor access to important resources.

**Example:**
Enable PIM for Azure AD roles, require approval for activating Global Admin.

**Reference:** [Azure AD PIM](https://learn.microsoft.com/en-us/azure/active-directory/privileged-identity-management/pim-configure)

---

### Just-in-Time (JIT) Access
- Grant privileged access only when needed, for a limited time.

**Example:**
Use PIM to activate the Owner role for 1 hour.

---

### Identity Protection
- Detects and responds to identity-based risks (e.g., leaked credentials, risky sign-ins).

**Example:**
Block sign-in for users with high-risk detected by Identity Protection.

**Reference:** [Azure AD Identity Protection](https://learn.microsoft.com/en-us/azure/active-directory/identity-protection/overview-identity-protection)

---

### Access Reviews
- Periodically review and certify user access to resources.

**Example:**
Set up quarterly access reviews for all users in the Contributors group.

**Reference:** [Access Reviews](https://learn.microsoft.com/en-us/azure/active-directory/governance/access-reviews-overview)

---

### Security Defaults and MFA Implementation
- Security defaults enforce best practices like MFA for all users.

**Example:**
Enable security defaults to require MFA for all admins.

**Reference:** [Security Defaults](https://learn.microsoft.com/en-us/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)

---

## 4. Integration Scenarios

### Hybrid Identity Solutions with AD Connect
- Synchronize on-premises AD with Azure AD.

**Example:**
Use Azure AD Connect to sync users and enable SSO for cloud apps.

**Reference:** [Azure AD Connect](https://learn.microsoft.com/en-us/azure/active-directory/hybrid/whatis-azure-ad-connect)

---

### Federation Services
- Use ADFS or third-party IdPs for SSO.

**Example:**
Configure Azure AD as a federated IdP with ADFS.

---

### Application Integration using App Registrations
- Register apps in Azure AD for authentication and API access.

**Example:**
Register a web API and configure permissions for client apps.

**Reference:** [App registrations](https://learn.microsoft.com/en-us/azure/active-directory/develop/quickstart-register-app)

---

### Managed Identities for Azure Resources
- Provide Azure services with an automatically managed identity.

**Example:**
Assign a managed identity to an Azure VM to access Key Vault.

**Reference:** [Managed Identities](https://learn.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview)

---

### Service Principals
- Non-human identity for apps/services to access Azure resources.

**Example:**
Create a service principal for a CI/CD pipeline to deploy resources.

**Reference:** [Service Principals](https://learn.microsoft.com/en-us/azure/active-directory/develop/app-objects-and-service-principals)

---

## 5. Best Practices

### Identity Governance
- Implement policies for lifecycle, access, and compliance.

**Example:**
Automate user provisioning and deprovisioning with access packages.

---

### Emergency Access Accounts
- Create break-glass accounts with strong authentication, not used daily.

**Example:**
Maintain two emergency accounts with Global Admin, exclude from MFA policies.

---

### Break-glass Procedures
- Document and test emergency access procedures.

---

### Monitoring and Auditing
- Enable Azure AD logs, integrate with SIEM.

**Example:**
Send sign-in logs to Azure Monitor or Sentinel.

---

### Compliance with Industry Standards
- Configure Azure AD and resources to meet SOC, ISO, GDPR requirements.

**Example:**
Enable auditing, data residency, and privacy controls.

**Reference:** [Azure Compliance Offerings](https://learn.microsoft.com/en-us/azure/compliance/)

---

*Let me know if you need code samples, ARM templates, or specific implementation steps for any of these topics.*
