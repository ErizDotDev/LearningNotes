# Virtual Networking

---

- Virtual networks and virtual subnets allows Azure resources (VMs, web apps, databases) to communicate with each other, with internet, and with on-cloud premises resources.
    - **Virtual network** refers to the underlying network infrastructure in the cloud which is isolated to other networks.
    - **Virtual subnet** is a smaller portion of a virtual network used for organizing resources in that smaller chunk of the network.
- Supports both private and public endpoints
    - **Public endpoints** have a public IP address that can be accessed from anywhere in the world.
    - **Private endpoints** exist within a virtual network and have a private IP address from within the address space of that virtual network.
- Offers the following network capabilities:

### Isolation and segmentation

- Each virtual network created is isolated from one another.
- A network can be created by providing a range of public or private IP address.
    - IP range will only exist within the virtual network and can’t be accessed from the internet.
    - This range can then be divided into subnets.
- Use Azure’s built-in name resolution service for name resolution needs.
    - It’s also possible to use external DNS service with a few changes in the configuration.

### Internet communications

- Possible by assigning a public IP address to a resource or putting the resource behind a public load balancer.

### Communicate between Azure resources

- Virtual networks are utilized not only by VMs but also by other Azure services such as Azure virtual machine scale sets.
- Service endpoints can be used to connect to databases and storage.
    - Enables multiple resources to be linked to the virtual networks to improve security and provide optimal routing between services.

### Communicate with on-premises resources

- There are three mechanisms available to achieve this:
    - **Point-to-site VPN connections**
        - From an outside (the organization) computer to corporate network.
        - Client computer initiates an encrypted VPN connection to connect to Azure virtual network.
    - **Site-to-site VPN**
        - Links on-premises VPN to Azure VPN Gateway in a virtual network
        - In effect, Azure resources will appear as if they were on the organization’s local network.
    - **Azure ExpressRoute** ^36b61a
        - Provides a dedicated private network that is not accessible via the Internet.
        - Useful for environments where a higher bandwidth and higher level of security is needed.
        - Azure’s equivalent of AWS Direct Connect.

### Route network traffic

- By default, Azure routes traffic between subnets on any connected virtual networks, on-premises network and internet.
- Traffic can also be controlled by overriding the settings using the following:
    - Route tables, which is used to define rules on how traffic should be directed.
    - Border Gateway Protocol (BGP) is used to spread BGP routes to Azure virtual networks.

### Filter network traffic

- Network security groups which contain multiple inbound and outbound security rules.
    - Used to allow or block traffic based on source and destination IP address, port and protocol.
- Network virtual appliances are specialized VMs used to carry out a particular network function such as running a firewall or performing WAN optimization.

### Connect virtual networks

- Link virtual networks together using virtual network peering.
- Connection is private and travels behind Microsoft’s backbone network.
- These virtual networks can be in different regions.
- User-defined routes (UDR) is used to control the routing tables between subnets within or between virtual networks.
    - Allows greater control on traffic flow.