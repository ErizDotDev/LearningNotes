# Azure VPN
---

- Uses an encrypted tunnel within another network to connect two or more trusted private networks over an untrusted network (public internet).
- Networks use VPN to share information in a secure and safe manner.

### VPN gateways

- Type of gateway deployed in a dedicated subnet of a virtual network and does the following:
    - Connect on-premises datacenter to virtual networks via site-to-site connection.
    - Connect individual devices to virtual networks through a point-to-site connection.
    - Connect virtual networks to other virtual networks through network-to-network connection.

- There can only be one gateway in each virtual network, but you can use this one gateway to connect to multiple locations.    
    - Having multiple gateways deployed is fine but it can cause significant amount of complexity to a cloud setup.

- VPN types can be determined based on which traffic needs what encryption.    
    - **Policy-based VPN gateways** specific statically the IP address of packets that should be encrypted through each tunnel in a policy.
        - It evaluates every data packet if it belongs to one of the IP addresses that was specified, and it selects which tunnel the packet will be sent through.

    - **Route-based gateways** - IP routes (static or dynamic routing protocols) decide which one of these tunnel interfaces to use when sending each packet.
        - Generally favored method for connecting on-premises devies to VPN.
        - They’re more resilient to changes like creating new subnets.
        - Use this if you have the following connectivity needs:
            - Connections between virtual networks
            - Point-to-site connections
            - Multisite connections
            - Coexistence with Azure ExpressRoute gateway

- For Azure, regardless of VPN type, the authentication method used is a pre-shared key.    
- It is also important to configure your VPN to make it highly available and fault tolerant. Here are a few ways to achieve this.
    

### Active/standby

- By default, there are two VPN gateway instances that are deployed, even if there is only one VPN gateway resource appearing in Azure.
- Should the first VPN gateway instance goes down, the second VPN gateway instance will stand in as the replacement.
- It will take a few seconds (maximum of 90 seconds) for the switch between the two instances to complete.

### Active/active

- Deploying an active/active configuration is made possible because of the introduction of BGP routing protocols.
- A public IP is assigned to each instance, and a tunnel from on-premises devices can be linked to these IP addresses.
- Availability can be improved by deploying an additional VPN device on premises.

### ExpressRoute failover

- VPN gateway can be used to act as a failover in the low chance that an ExpressRoute circuit goes down.
    - An ExpressRoute circuit can go down due to certain issues that can affect the physical network infrastructure that powers these services.
- The network path will switch from private (ExpressRoute) to public (internet) when the failover occurs which ensures high availability.

### Zone-redundant gateway

- Ensure high availability in a zone-level if a region supports availability zones.
- Protects gateways from zone level failure, (e.g. one availability zone goes down)
- VPN gateways can be deployed per availability zone within a region.
- This configuration will require different SKUs (stock keeping units) for each gateway and will require the use of Standard IP address from Basic IP address.