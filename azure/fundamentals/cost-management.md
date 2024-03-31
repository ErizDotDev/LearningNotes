# 🕋 Cost management

Overview
	[[cost-management#Resource type|Resource type]]
	[[cost-management#Consumption|Consumption]]
	[[cost-management#Maintenance|Maintenance]]
	[[cost-management#Geography|Geography]]
	[[cost-management#Network Traffic|Network Traffic]]
	[[cost-management#Subscription Type|Subscription Type]]
	[[cost-management#Azure marketplace|Azure marketplace]]
[[cost-management#Pricing and Total Cost of Ownership Calculators|Pricing and Total Cost of Ownership Calculators]]
	[[cost-management#Pricing calculator|Pricing calculator]]
	[[cost-management#TCO calculator|TCO calculator]]
[[cost-management#MS Cost Management Tool|MS Cost Management Tool]]
	[[cost-management#Cost management|Cost management]]
	[[cost-management#Cost alerts|Cost alerts]]
	[[cost-management#Budgets|Budgets]]
[[cost-management#Purpose of tags|Purpose of tags]]
	[[cost-management#Resource tag management|Resource tag management]]
	[[cost-management#Example tagging structure|Example tagging structure]]
	
- Operation Expenses (OpEx) cost can be impacted factors including:

### Resource type

- Type of resource, settings of resource and Azure region affects cost.
- Azure creates meters for that resource which will track the resource usage and generate a usage record used to calculate your bill.
- Examples:
    - For storage, the storage type (e.g. blob), performance tier, access tier, redundancy affects cost.
    - For VMs, the licensing of the OS, processor and number of cores, attached storage and network interface affect cost.

### Consumption

- Pay-as-you-go - The more compute is used, the more it costs. The less resource used, the less it costs.
- Azure offers pricing where a certain set amount of resource is used in a given period, and discounts will be applied to its pricing, up to 72%.
- You may also choose to reserve capacity where you’re committing to use and pay for a certain amount of Azure resource for a given period.
    - If sudden surge in demand exceeds the amount pre-reserved, pay the additional resources that exceeded your reservation.

### Maintenance

- Scenarios happen where when a VM is provisioned, additional resources like network interfaces and storage are also provisioned.
- If we deprovision this VM, these additional resources may not be deprovisioned at the same time.
- Keep track of all unused resources to control your costs.
- Maintain your cloud environment.

### Geography

- Azure resources can be distributed globally, making it easier for your customers to access your application/resources.
- Global deployment comes global pricing differences which is affected by factors such as cost of power, labor and tax.
- Network traffic is also impacted.
    - It is cost effective to transfer data within Europe than to transfer data from Europe to Asia or other region.

### Network Traffic

- Bandwidth refers to data moving in and out of datacenter.
- Inbound data transfers are free.
- Outbound data transfers have pricing depending on zones.
    - **Zone** is a geographical grouping of Azure regions for billing purposes.

### Subscription Type

- Some Azure subscription types include usage allowance which affects cost.
- Example: Azure free usage for 12 months; Additional credit within first 30 days of sign-up.

### Azure marketplace

- Place where you can purchase Azure based solutions from third party vendors.
    - Example of solutions that can be purchased:
        - Server with software pre-installed or configured.
- Services and expertise of vendors can also be purchased along with their product.
    - Pricing structure is dictated by vendors.
- The solutions in marketplace are certified and compliant with Azure standards.

---

## Pricing and Total Cost of Ownership Calculators

### Pricing calculator

- Gives an estimated cost of provisioning resources in Azure.
    - Get an estimate for individual resources, built-out solutions or example scenarios.
    - Get estimates for any provisioned resources while also accounting for different configurations and settings possible for each resource.

### TCO calculator

- Gives you a cost comparison of running resources in an on-prem environment vs. running resources in Azure.
- Provide all on-prem hosted resources like servers, databases, storage and outbound network traffic, as well as factoring in assumptions like power and IT labor costs and the calculator will compute how your on-prem setup will cost when hosted in Azure.

---

## MS Cost Management Tool

- Service that help inform you about new resources that may be accidentally provisioned.

### Cost management

- Provides ability to:
    - Check Azure resource costs quickly
    - Create alerts based on resource spend
    - Create budgets to automate management of resources
- **Cost analysis** provides quick visuals of your Azure costs that can be customized depending on billing cycle, region, resource, and so on.
    - Views can also be aggregated by organization/teams.

### Cost alerts

- Provides a single location to check all alert types that can pop out in Cost Management service.
- Alerts are displayed in Azure portal and sent on email based on distro lists created per budget.
- Three types include:
    - **Budget alerts**
        - Notify you when spending based on usage or cost if the limit set is reached or exceeded
    - **Credit alerts**
        - Notify you when your Azure monetary commitments are consumed.
            - Monetary commitments are only for organizations with Enterprise Agreements with Azure.
        - Generated automatically when usage is at 90% and 100% of the allocated credit.
    - **Department spending quota alerts**
        - Notifies when a department reaches a fixed threshold of quota (Example: 50 or 75% of quota)

### Budgets

- Set spending limit for Azure.
- Set budgets based on subscription, resource group, service type or other criteria.
- Budget alert is created by default when a budget is created.
- Advanced use of budget involves creating automated process of turning resources on/off once a budget alert has been generated.

---

## Purpose of tags

- Cloud usage grows so it’s important to keep your resources organized to help you understand your cloud usage and manage costs.
- Organization strategies to store related resources:
    - Using subscriptions
    - Using resource groups
    - Resource tags
- **Tags** provide extra information or metadata to resources.
- Tags are useful for:
    - **Resource management**. Enable you to locate and perform actions on resources associated with specific workloads, environments, business units and owners.
    - **Cost management and optimization.**
    - **Operations management.** Group resources depending on how critical their availability are to the business.
        - Helps generate a business’ SLA.
    - **Security.** Classify data by security level (e.g. public/confidential)
    - **Governance and regulatory compliance.** Scenarios where resources are tagged by department or owner.
    - **Workload optimization and automation.** Help label resources involved in complex deployments and automation processes.

### Resource tag management

- Use Azure Policy to enforce tagging rules and conventions.
    - Example: Strict enforcement of adding tags to newly provisioned resources.
- Resources don’t inherit tags from subscriptions or resource groups.
    - This will allow you to create custom tagging schema that can change depending on the level.

### Example tagging structure

- Create tags for following metadata:
    - `AppName`
    - `CostCenter` - Cost center code
    - `Owner`
    - `Environment` - Dev, Test or Prod
    - `Impact`
- Remember that you may not add a tag to all resources.
    - Example: Not adding an Impact tag to some resources means they are not mission critical while resources that have the Impact tag are mission critical.