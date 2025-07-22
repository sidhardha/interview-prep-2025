# 30-Day Microsoft Azure Learning Roadmap (Printable Checklist)

**Commitment:** 2-3 hours/day  
**Focus:** Security, Networking, Architecture, Development  
**Certifications:** AZ-500, AZ-700, AZ-305  
**Resources Needed:** Azure Free Tier, Microsoft Learn, VS Code, Azure CLI, GitHub/Azure DevOps

---

## Progress Tracker
- [ ] Create Azure Free Tier account
- [ ] Set up Microsoft Learn profile
- [ ] Install VS Code + Azure extensions
- [ ] Install Azure CLI
- [ ] Create GitHub/Azure DevOps repo for notes/labs

---

## Week 1: Azure Fundamentals & Security Basics

### Day 1
- [ ] **Core Topic:** Azure AD Concepts, RBAC
- [ ] **Lab:** Configure RBAC for a resource group ([MS Learn: RBAC](https://learn.microsoft.com/en-us/azure/role-based-access-control/overview))
- [ ] **Docs:** [Azure AD Overview](https://learn.microsoft.com/en-us/azure/active-directory/fundamentals/active-directory-whatis)
- [ ] **Progress:** Document RBAC setup in repo

### Day 2
- [ ] **Core Topic:** Azure Subscriptions, Management Groups
- [ ] **Lab:** Organize resources with management groups ([MS Learn](https://learn.microsoft.com/en-us/azure/governance/management-groups/overview))
- [ ] **Docs:** [Subscriptions](https://learn.microsoft.com/en-us/azure/cost-management-billing/manage/create-subscription)
- [ ] **Progress:** Screenshot management group structure

### Day 3
- [ ] **Core Topic:** Azure Resource Manager (ARM)
- [ ] **Lab:** Deploy resources using ARM templates ([MS Learn: ARM](https://learn.microsoft.com/en-us/azure/azure-resource-manager/templates/overview))
- [ ] **Docs:** [ARM Documentation](https://learn.microsoft.com/en-us/azure/azure-resource-manager/)
- [ ] **Progress:** Commit ARM template to repo

### Day 4
- [ ] **Core Topic:** Azure Security Center & Defender
- [ ] **Lab:** Enable Security Center, review recommendations ([MS Learn: Security Center](https://learn.microsoft.com/en-us/azure/security-center/security-center-introduction))
- [ ] **Docs:** [Defender for Cloud](https://learn.microsoft.com/en-us/azure/defender-for-cloud/)
- [ ] **Progress:** Note security recommendations

### Day 5
- [ ] **Core Topic:** Azure Key Vault & Encryption
- [ ] **Lab:** Store and retrieve secrets ([MS Learn: Key Vault](https://learn.microsoft.com/en-us/azure/key-vault/general/overview))
- [ ] **Docs:** [Key Vault Documentation](https://learn.microsoft.com/en-us/azure/key-vault/)
- [ ] **Progress:** Secure a secret, document process

### Day 6
- [ ] **Core Topic:** Compliance & Governance (Policy, Blueprints)
- [ ] **Lab:** Apply a policy to restrict resource types ([MS Learn: Policy](https://learn.microsoft.com/en-us/azure/governance/policy/overview))
- [ ] **Docs:** [Azure Blueprints](https://learn.microsoft.com/en-us/azure/governance/blueprints/overview)
- [ ] **Progress:** Policy assignment evidence

### Day 7
- [ ] **Project:** Secure Resource Group Deployment
- [ ] **Tasks:**
    - Deploy a resource group with RBAC, policy, and Key Vault
    - Document steps in repo
    - **Checkpoint:** Review week’s labs, take [MS Learn Knowledge Check](https://learn.microsoft.com/en-us/training/azure/)

---

## Week 2: Azure Networking

### Day 8
- [ ] **Core Topic:** Virtual Networks & Subnets
- [ ] **Lab:** Create VNet, subnets ([MS Learn: VNets](https://learn.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview))
- [ ] **Docs:** [VNet Documentation](https://learn.microsoft.com/en-us/azure/virtual-network/)
- [ ] **Progress:** Diagram VNet/subnet structure

### Day 9
- [ ] **Core Topic:** Network Security Groups (NSG)
- [ ] **Lab:** Configure NSG rules ([MS Learn: NSG](https://learn.microsoft.com/en-us/azure/virtual-network/network-security-groups-overview))
- [ ] **Docs:** [NSG Documentation](https://learn.microsoft.com/en-us/azure/virtual-network/network-security-groups-overview)
- [ ] **Progress:** Document NSG rules

### Day 10
- [ ] **Core Topic:** Azure Firewalls & DDoS Protection
- [ ] **Lab:** Deploy Azure Firewall, enable DDoS ([MS Learn: Firewall](https://learn.microsoft.com/en-us/azure/firewall/overview))
- [ ] **Docs:** [DDoS Protection](https://learn.microsoft.com/en-us/azure/ddos-protection/ddos-protection-overview)
- [ ] **Progress:** Screenshot firewall config

### Day 11
- [ ] **Core Topic:** Load Balancing (ALB, NLB)
- [ ] **Lab:** Deploy and test Azure Load Balancer ([MS Learn: Load Balancer](https://learn.microsoft.com/en-us/azure/load-balancer/load-balancer-overview))
- [ ] **Docs:** [App Gateway](https://learn.microsoft.com/en-us/azure/application-gateway/overview)
- [ ] **Progress:** Document load balancer test

### Day 12
- [ ] **Core Topic:** Azure DNS & Traffic Manager
- [ ] **Lab:** Configure custom DNS, Traffic Manager ([MS Learn: DNS](https://learn.microsoft.com/en-us/azure/dns/dns-overview))
- [ ] **Docs:** [Traffic Manager](https://learn.microsoft.com/en-us/azure/traffic-manager/traffic-manager-overview)
- [ ] **Progress:** Record DNS/Traffic Manager setup

### Day 13
- [ ] **Core Topic:** Hybrid Connectivity (VPN, ExpressRoute)
- [ ] **Lab:** Set up site-to-site VPN ([MS Learn: VPN Gateway](https://learn.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways))
- [ ] **Docs:** [ExpressRoute](https://learn.microsoft.com/en-us/azure/expressroute/expressroute-introduction)
- [ ] **Progress:** Note VPN/ExpressRoute config

### Day 14
- [ ] **Project:** Secure, Load-Balanced Web App
- [ ] **Tasks:**
    - Deploy web app behind load balancer, secure with NSG
    - Document architecture in repo
    - **Checkpoint:** Take Azure networking practice test

---

## Week 3: Azure Architecture & High Availability

### Day 15
- [ ] **Core Topic:** Well-Architected Framework
- [ ] **Lab:** Review and apply WAF pillars ([MS Learn: WAF](https://learn.microsoft.com/en-us/azure/architecture/framework/))
- [ ] **Docs:** [WAF Documentation](https://learn.microsoft.com/en-us/azure/architecture/framework/)
- [ ] **Progress:** WAF checklist in repo

### Day 16
- [ ] **Core Topic:** Solution Design Patterns
- [ ] **Lab:** Implement reference architecture ([MS Learn: Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/))
- [ ] **Docs:** [Design Patterns](https://learn.microsoft.com/en-us/azure/architecture/patterns/)
- [ ] **Progress:** Diagram solution pattern

### Day 17
- [ ] **Core Topic:** High Availability (HA) & Disaster Recovery (DR)
- [ ] **Lab:** Deploy VM scale set, configure backup ([MS Learn: HA/DR](https://learn.microsoft.com/en-us/azure/architecture/resiliency/))
- [ ] **Docs:** [Azure Backup](https://learn.microsoft.com/en-us/azure/backup/)
- [ ] **Progress:** Document HA/DR config

### Day 18
- [ ] **Core Topic:** Cost Management & Optimization
- [ ] **Lab:** Analyze and optimize costs ([MS Learn: Cost Management](https://learn.microsoft.com/en-us/azure/cost-management-billing/))
- [ ] **Docs:** [Cost Optimization](https://learn.microsoft.com/en-us/azure/architecture/framework/cost/)
- [ ] **Progress:** Cost report in repo

### Day 19
- [ ] **Core Topic:** Monitoring & Logging (Azure Monitor, Log Analytics)
- [ ] **Lab:** Set up monitoring for resources ([MS Learn: Monitor](https://learn.microsoft.com/en-us/azure/azure-monitor/overview))
- [ ] **Docs:** [Log Analytics](https://learn.microsoft.com/en-us/azure/azure-monitor/logs/log-analytics-overview)
- [ ] **Progress:** Save monitoring dashboard

### Day 20
- [ ] **Core Topic:** Application Insights & Alerts
- [ ] **Lab:** Enable App Insights for web app ([MS Learn: App Insights](https://learn.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview))
- [ ] **Docs:** [Alerts](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-overview)
- [ ] **Progress:** Document alert setup

### Day 21
- [ ] **Project:** Highly Available, Monitored Solution
- [ ] **Tasks:**
    - Deploy solution with HA, monitoring, cost controls
    - Document in repo
    - **Checkpoint:** Take architecture practice test

---

## Week 4: Azure Development, DevOps & Containers

### Day 22
- [ ] **Core Topic:** Infrastructure as Code (ARM, Bicep)
- [ ] **Lab:** Deploy resources with Bicep ([MS Learn: Bicep](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/overview))
- [ ] **Docs:** [Bicep Documentation](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/))
- [ ] **Progress:** Commit Bicep file to repo

### Day 23
- [ ] **Core Topic:** Azure DevOps Fundamentals
- [ ] **Lab:** Set up Azure DevOps project, CI/CD pipeline ([MS Learn: DevOps](https://learn.microsoft.com/en-us/azure/devops/learn/what-is-azure-devops))
- [ ] **Docs:** [DevOps Documentation](https://learn.microsoft.com/en-us/azure/devops/)
- [ ] **Progress:** Pipeline YAML in repo

### Day 24
- [ ] **Core Topic:** Azure SDKs & APIs
- [ ] **Lab:** Use Azure SDK in sample app ([MS Learn: SDKs](https://learn.microsoft.com/en-us/azure/developer/))
- [ ] **Docs:** [Azure REST API](https://learn.microsoft.com/en-us/rest/api/azure/)
- [ ] **Progress:** Commit sample code

### Day 25
- [ ] **Core Topic:** Containerization (Docker, ACR)
- [ ] **Lab:** Build/push image to Azure Container Registry ([MS Learn: ACR](https://learn.microsoft.com/en-us/azure/container-registry/container-registry-intro))
- [ ] **Docs:** [Container Registry](https://learn.microsoft.com/en-us/azure/container-registry/)
- [ ] **Progress:** Image in ACR, document steps

### Day 26
- [ ] **Core Topic:** Azure Kubernetes Service (AKS)
- [ ] **Lab:** Deploy app to AKS ([MS Learn: AKS](https://learn.microsoft.com/en-us/azure/aks/))
- [ ] **Docs:** [AKS Documentation](https://learn.microsoft.com/en-us/azure/aks/)
- [ ] **Progress:** Screenshot AKS deployment

### Day 27
- [ ] **Core Topic:** Serverless (Azure Functions, Logic Apps)
- [ ] **Lab:** Create and deploy Azure Function ([MS Learn: Functions](https://learn.microsoft.com/en-us/azure/azure-functions/))
- [ ] **Docs:** [Logic Apps](https://learn.microsoft.com/en-us/azure/logic-apps/)
- [ ] **Progress:** Function code in repo

### Day 28
- [ ] **Project:** End-to-End DevOps Pipeline
- [ ] **Tasks:**
    - Deploy app using IaC, CI/CD, containerization, monitoring
    - Document pipeline in repo
    - **Checkpoint:** Take DevOps/containerization practice test

---

## Week 5: Review, Assessment & Capstone

### Day 29
- [ ] **Core Topic:** Review & Practice Assessments
- [ ] **Lab:** Take official practice tests ([MS Learn: Practice Assessments](https://learn.microsoft.com/en-us/certifications/resources/))
- [ ] **Docs:** Review weak areas
- [ ] **Progress:** Note scores, plan for improvement

### Day 30
- [ ] **Capstone Project:** Deploy Secure, Scalable Solution
- [ ] **Tasks:**
    - Design, deploy, and document a secure, scalable Azure solution using skills from previous weeks
    - Present repo and documentation
    - **Success Criteria:**
        - [ ] All daily labs complete
        - [ ] Capstone deployed and documented
        - [ ] Practice assessments passed
        - [ ] Repo up to date

---

**Tips:**
- Use [Microsoft Learn](https://learn.microsoft.com/en-us/training/azure/) for all modules
- Document every lab in your repo
- Take screenshots for evidence
- Review and retake practice tests as needed

---

**Printable Checklist:**
- [ ] Week 1 complete
- [ ] Week 2 complete
- [ ] Week 3 complete
- [ ] Week 4 complete
- [ ] Capstone complete
- [ ] All progress documented

---

This roadmap is suitable for both beginners and those with some Azure experience, focusing on practical skills, real-world applications, and certification readiness. Each day builds on the previous, ensuring a comprehensive Azure skillset.
