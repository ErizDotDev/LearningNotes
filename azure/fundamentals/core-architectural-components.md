#Azure #Cloud #Fundamentals
# 🕋 Core architectural components
---

[[core-architectural-components#Accounts|Accounts]]
[[core-architectural-components#Physical Infrastructure|Physical Infrastructure]]
	[[core-architectural-components#Regions|Regions]]
	[[core-architectural-components#Availability Zones|Availability Zones]]
	[[core-architectural-components#Region Pairs|Region Pairs]]
	[[core-architectural-components#Sovereign Regions|Sovereign Regions]]
[[core-architectural-components#Management Infrastructure|Management Infrastructure]]
	[[core-architectural-components#Resource|Resource]]
	[[core-architectural-components#Resource Groups|Resource Groups]]
	[[core-architectural-components#Subscriptions|Subscriptions]]
	[[core-architectural-components#Management Groups|Management Groups]]
[[core-architectural-components#Provisioning a resource|Provisioning a resource]]

## Accounts

- Runs in a subscription model.
    - After creating an account in Azure, a subscription was automatically created to support that account.
    - A user or an organization can choose to create multiple subscriptions based on its needs.
        - Example: An organization uses one subscription for business, one subscription for development, one subscription for marketing, etc.

## Physical Infrastructure

### Regions
^e6a4db

- At least one, or multiple data centers placed in different parts of the world.
- Characterized by countries or continent sections (e.g. east-asia, west-us) as names.

### Availability Zones
^a6b124

- Logical grouping of data centers created to ensure that cloud services are always available.
- Characterized by cities under a certain country/continent/region.
- Primarily utilized by resources like VMs, load balancers, disks and databases.
- Categories:
    - Zonal services: Pin resource to a specific zone (e.g. VMs, disks, IP addresses)
    - Zone-redundant services: Azure replicates resources automatically across zones (e.g. database)
    - Non-zonal services: Services that are always available and resilient to zone-wide outages.

### Region Pairs
^5667e3

- Provides additional resiliency to services deployed in the cloud.
- Regions are partnered to other regions within the same geography.
    - Example: East US and West US, East Asia and West Asia
- If one of the regions failed for whatever reason, fail over will fall to the other region.
- Advantages:
    - If an extended outage occurs within Azure, one of the region pairs is prioritized so that services can get back up quickly.
    - Azure updates are done at a time per region pair to minimize downtime.
- Pairings are not always bi-directional. For example, South India pairs with West India, but West India is not region paired with South India. Instead, West India is paired with Central India.

### Sovereign Regions
^ecc128

- Azure instances that are isolated from the main Azure instances.
- Used for compliance and legal reasons.
- Example: US governmental regions, China.

## Management Infrastructure

### Resource

- Anything that can be created, provisioned or deployed.
- Example: VM, IP address, databases, virtual networks, etc.

### Resource Groups

- Provides groupings for resources.
    - If an action was executed in one resource group, all resources inside the resource group will be affected.
    - If a resource group is deleted, all resources inside it will also be deleted.
    - If a resource group has been granted or denied access, the contained resources within this group will inherit those access permission.
- Rules:
    - Resource can only be a part of a single resource group.
    - Resource can move between resource groups.
        - If a resource is moved from one group to another, the resource cannot be associated anymore to the former resource group.
    - Resource groups can contain multiple resources.
    - Resource groups can’t be nested.

### Subscriptions

- Subscriptions can be used to define boundaries between Azure services, resources, etc.
- Boundary types:
    - Billing boundary - Create multiple subscriptions for different billing requirements (e.g. one subscription can go as high as $10,000 per month while the other subscription is limited only to $1,000 per month)
    - Access control boundary - Applies access management policies at the subscription level.
        - Example: A company that has different departments (DevOps, Development, IT Infrastructure) have their own subscriptions each with different access level to different resources that the company provisions.
- Different setups:
    - Environment - Development, testing, security
    - Organization structure - Subscription per team/department
    - Billing - One subscription for production workload, another subscription for development and testing workload
- Can only support one parent.

### Management Groups

- The tier above subscriptions
- Resources → Resource Groups → Subscriptions → Management groups
- Works like Resource groups in principle.
- Management groups can be nested.
- Can support up to 6 levels of depth.
- Can only support one parent.

## Provisioning a resource

- When a virtual machine is created, other resources are being created behind the scenes.
- Here are some of the resources that were created:
    - Storage account
    - Public IP address
    - Network security group
    - Virtual network
    - Network interface
    - Disk