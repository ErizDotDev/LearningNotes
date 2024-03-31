# 🕋 Compute and networking services

---

## Virtual Machine

- Azure’s version of AWS EC2

### Virtual machine scale sets

- Azure automates the process of creating multiple VMs and setting them up for load balancing with Virtual machine scale sets.
- The number of VM instances can increase, or decrease in response to demand; or
- Scale the number of VM instances on a defined schedule.
- Automatically deploys load balancer.

### Virtual machine availability sets

- Free!
- Designed to ensure that VMs receive updates in intervals and have varied power and network connectivity to prevent all VMs from being unavailable due to power or network outage.
- VMs are grouped in two ways:
    - **Update domain**
        - Groups VMs that are going to be rebooted at the same time.
        - Useful when applying updates to VMs - VMs in an update domain grouping will go offline at the same time to receive the updates while another group remains online.
            - A 30 minute recovery period will take effect before the other update domain group receives the update.
    - **Fault domain**
        - Groups VMs by common power source and network switch.
        - Ensures that VMs will not go down simultaneously because each VM is separated into different power and network switches.

## Virtual Desktop

- Azure’s version of AWS Workspaces
- Enables users access to Windows from any location as it is hosted in the cloud.

## Azure Containers

- Use case: Splitting a website into three logical sections: the backend, the frontend and storage.
    - Each section can then be maintained, scaled or updated independently.
    - BE can be scaled if it reaches capacity while leaving the FE and storage untouched.
    - Updates on one component does not affect the other.

### Azure Container Instances

- Serverless service that enables user to run containers quickly

### Azure Container Apps

- Similar to Azure Container Instances but with additional orchestration features such as scaling and load balancing.
- Azure’s version of AWS Fargate.

## Azure Functions

^ed9e52

- Azure’s version of AWS Lambda.
- Serverless and event-driven.
- An event will execute the code/function, most of the time a REST API request.
- Useful for saving resources because no resources will be used if there’s no event being triggered.
    - Resources are deallocated once the function finishes running.
- Good choice when demand is variable because it scales automatically based on demand.
- Can be stateless or stateful
    - **Stateless** - Function always restarts every time it responds to an event.
    - **Stateful** - A context is passed through the function that tracks prior activity
        - Also called as Durable Functions.

## App Service

- Azure’s version of AWS Elastic Beanstalk.
- HTTP-based service that offers a build and hosting platform for web apps without managing the underlying infrastructure.
- Offers scaling and high availability via load balancing and traffic manager.
- Supports both Windows and Linux and multiple languages.
- Enables automated deployments from various online code repositories.
- Types of app services
    - Web Apps
    - API Apps
    - WebJobs
        - Used to run a program (.exe) or a script (.cmd, .bat, Bash, Powershell)
        - Cab be scheduled or run via a trigger
        - Used to run background tasks as part of application logic.
    - Mobile Apps

## Virtual Networking

[[azure/fundamentals/compute-networking-services/virtual-networking|Virtual Networking]]

### Typical workflow for creating VMs and basic network setup

- Create VM
- Check VM’s assigned public IP address
- Create NSG rule for HTTP access
    - This means creating a rule that will allow access to port 80
    - By default, created Linux VMs only allow port 22 (SSH access)
- Check if IP is being accessed

## Azure VPN

[[azure/fundamentals/compute-networking-services/azure-vpn|Azure VPN]]

## Azure ExpressRoute

[[virtual-networking#^36b61a|Initial information]]

- Uses **ExpressRoute Circuit**, a connectivity provider to connect on-premises networks to MS cloud services (Azure and Office365) via a private connection.
- Connection in ExpressRoute will never go through the public internet.
- Can accept any form of connection, from any-to-any (public IP) network, to a point-to-point Ethernet network, or a complex virtual cross-connection.

### Benefits and features

**Connectivity to MS cloud services**

- Enables direct access to the following services in all regions:
    - MS Office 365
    - MS Dynamics 365
    - Azure compute services (virtual machines)
    - Azure cloud services (Azure Storage, Cosmos DB)

**Global connectivity**

- Enable ExpressRoute Global Reach to exchange data across your network/on-prem servers through ExpressRoute circuit.
- Use case: Connect two facilities, an Asia office and a data center in Europe, with ExpressRoute circuits to MS network - while transferring the data outside of the public Internet.

**Dynamic routing**

- Uses BGP (Border Gateway Protocol) to exchange routes between on-premises networks and resources running in Azure.
    - This is an example of dynamic routing.

**Built-in redundancy**

- Ensures high availability by using redundancy devices.
- To complement this feature, you can configure multiple circuits.

### Connectivity Models

**CloudExchange Colocation**

- Colocation meaning your office, or data center are being hosted or co-located in a cloud exchange facility, like an ISP for example.

**Point-to-point Ethernet connection**

- Using a point-to-point connection to connect to the cloud.

**Any-to-any networks**

- Integrate WAN networks with Azure by connecting to the office or datacenters.
- Azure will integrate with your WAN to provide a connection that is similar to the one with local office to your data center.

**Directly from ExpressRoute sites**

- ExpressRoute Direct (directly connecting to ExpressRoute) provides fast network speeds and supports active/active connectivity at scale.

### Security considerations

- Even though most of your communication is happening behind the public internet, other information such as DNS queries, certificate revocation list checking and Azure CDN are all still transported via the public internet.

## Azure DNS

- **DNS (Domain Name Service)** - Translates the website address to its equivalent numerical IP address.
- Hosting service for DNS domains using Azure infrastructure.
- Its benefit is mostly accessing your DNS records with the same credentials as all the other Azure services.

### Benefits

**Reliability and performance**

- The domains are hosted in Azure’s global network of DNS name servers, which makes it highly available and resilient.
- Uses anycast networking, meaning a DNS query is answered by the closest available DNS server.

**Security**

- Based on Azure Resource Manager:
    - Has role-based access control to limit those who can access it.
    - Has activity logs to monitor how a user updated a particular resource or find an error when troubleshooting.
    - Resource locking to prevent anyone from accidentally deleting or modifying resources.

**Ease of use**

- Can manage DNS records Azure services as well as an organizations outside resources.
- Integrated in the Azure suit of services and uses the same credential as other Azure services.
- Automated DNS management can be achieved by using Azure SDKs.

**Customizable virtual networks with private domains**

- Also support private DNS domains.
- Use custom domain names in the private virtual network instead of the Azure provided ones.

**Alias records**

- Also supports alias record sets, primarily used to refer to Azure resource.
- If the address of a resource changes, alias record set seamlessly updates itself during DNS resolution.
- The alias record set points to a service instance, and the service instance is associated with an IP address.