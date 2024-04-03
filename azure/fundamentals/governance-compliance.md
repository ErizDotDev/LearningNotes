# 🕋 Governance and compliance
---

[[governance-compliance#Microsoft Purview|Microsoft Purview]]
	[[governance-compliance#Risk and compliance solutions|Risk and compliance solutions]]
	[[governance-compliance#Unified data governance|Unified data governance]]
[[governance-compliance#Azure Policy|Azure Policy]]
	[[governance-compliance#How does Azure Policy define policies?|How does Azure Policy define policies?]]
	[[governance-compliance#Initiatives|Initiatives]]
[[governance-compliance#Resource Locks|Resource Locks]]
[[governance-compliance#Service trust portal|Service trust portal]]
	[[governance-compliance#Features|Features]]

## Microsoft Purview

- Displays data and analytics about your on-prem, multi-cloud and SaaS data together.
- Features
    - Automated data discovery
    - Sensitive data classification
    - End-to-end data lineage
        - Tracks how data flows through your systems and applicaitons
- Two main solution areas:
    - Risk and compliance
    - Unified data governance

### Risk and compliance solutions

- Mainly MS 365 services like Teams, OneDrive, Exchange, etc that acts as primary data sources of your organization.
- Manages and monitors data.
- Helps your organization:
    - Protect sensitive data across clouds, apps, or services.
    - Identify risks in data and manage regulatory compliance requirements.
    - Get started with regulatory compliance.

### Unified data governance

- Manage data stored in Azure, SQL and other clouds like AWS S3.
- Enables your organization to:
    - Creates an up-to-date map of all your data in the defined platforms (single cloud, multi cloud) which includes data classification
    - Identify where sensitive data is stored in your platforms
    - Create a safe environment for users to find and consume important data
    - Generates insight on how your data is stored and used
    - Manage access to data securely and at scale.

---

## Azure Policy

- Azure service that helps you create, assign and manage policies that control or audit your resources.
    - Policies enforce different rules across all of your resource configurations in order to maintain the compliance of these configurations to standards.

### How does Azure Policy define policies?

- Enables the definition of individual policies and groups of policies, aka **initiatives**.
- Evaluates your resources then highlights resources that are not up to standard.
- Prevents non-compliant resources from being created.
- Policies can be set on every level (resources, resource groups, subscription, etc.) and are inherited based on policies set on the level above the target assignment.
- Different Azure services have built in policy and initiative definitions
    - If a policy is created that only allows a certain size of VM to be used in your environment, that policy is invoked when creating new VMs and evaluating VMs that are created before the policy is created.
- Azure Policy automatically fixes resources and configurations that are non-compliant to the policies.
    - Example: A certain group does not have a certain tag which according to a policy all resources should have. Azure Policy fixes that by automatically adding that tag.
    - You can choose to exempt some resources from this auto-fix feature by flagging them as exceptions to the policy.
- Integrates with ADO (Azure DevOps) to enforce standards on pre and post deployment phase of maintained applications.

### Initiatives

- Way of grouping related policies together.
- Example: There is a policy named Enable Monitoring in Azure Security Center whose goal is to monitor security recommendations for all resources in Security Center.
    - Under this initiative are the following policies:
        - Monitor unencrypted SQL databases
        - Monitor OS vulnerabilities
        - Monitor missing Endpoint protection
        - And there are hundreds more...

---

## Resource Locks

- Prevents resources from accidentally being deleted or updated
    - Even with RBAC policies in place, some person high in the organization with all access can still update or delete crucial resources.
- Applies at every Azure resource level and can also be inherited like policies.
- Resource lock types:
    - Delete
    - ReadOnly
- Resource locks are managed at the Settings tab of each resource
- To modify a locked resource, follow this two-step process:
    - Remove lock
    - Apply any action that your account is allowed to do.

---

## Service trust portal

- Portal that hosts references and various tools relating to security, governance and compliance in MS and Azure.
- Contains information about how MS protects its cloud services and data hosted in it through its own standard processes.

### Features

- You can save the documents hosted in this portal for your compliance requirements.
    - Files will be stored in the My Library feature.
- All Files should give you access to all compliance documents needed.
- Documents are available for 12 months after publishing or until a new version is available.