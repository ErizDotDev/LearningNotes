# 🕋 Identity, access, and security

[[identity-access-security#Azure directory services|Azure directory services]]
	[[identity-access-security|What does MS Entra ID do?]]
	[[identity-access-security#Connecting MS Entra ID with AD|Connecting MS Entra ID with AD]]
	[[identity-access-security#MS Entra Domain Services|MS Entra Domain Services]]
[[identity-access-security#Azure authentication methods|Azure authentication methods]]
	[[identity-access-security#Passwordless authentication|Passwordless authentication]]
[[identity-access-security#Azure external identities|Azure external identities]]
	[[identity-access-security#Features|Features]]
[[identity-access-security#Azure conditional access|Azure conditional access]]
	[[identity-access-security#When to use?|When to use?]]
[[identity-access-security#Role-based access control|Role-based access control]]
	[[identity-access-security#How is RBAC enforced?|How is RBAC enforced?]]
[[identity-access-security#Zero trust model|Zero trust model]]
[[identity-access-security#Defense in depth|Defense in depth]]
[[identity-access-security#Layers|Layers]]
[[identity-access-security#Microsoft Defender for Cloud|Microsoft Defender for Cloud]]
	[[identity-access-security#Azure-native protections|Azure-native protections]]
	[[identity-access-security#Defend hybrid resources|Defend hybrid resources]]
	[[identity-access-security#Defend resources on other clouds|Defend resources on other clouds]]
	[[identity-access-security#Assess, Secure and Defend|Assess, Secure and Defend]]
	
## Azure directory services

- Microsoft Entra ID
- Azure’s version of AWS IAM.
- Enables you to login and access both MS cloud applications and your own cloud applications.
- Helps maintain on-prem Active Directory (AD) deployment.
- If Active Directory on Windows Server is the on-prem solution, MS Entra ID is the cloud solution for identity and access management.
- AD can be integrated with Entra ID to help monitor suspicious sign-in attempts for FREE.
    - AD on MS Windows Server does not monitor suspicious sign-in attempts.
- Anyone in an organization can use Microsoft Entra ID.

### What does MS Entra ID do?

- **Authentication.** Provides features such as self-service password reset, multifactor authentication, etc.
- **Single Sign On (SSO).** Enables you to remember a single set of credentials (username and password) to login to multiple applications. An identity is tied to a user and this identity makes it easy to restrict/enable access to various systems.
- **Application management.** Provides access to My Apps portal that contains all applications that an organization is maybe using.
- **Device Management.** Devices can be managed through MS Intune. Helps with allowing access to only known and allowed devices.

### Connecting MS Entra ID with AD

- Possible but needs to maintain two identity sets, one for MS Entra ID and one for AD
- Uses MS Entra Connect to connect the two identity systems, sync any changes happening between the two systems and enables the use of features like multifactor authentication, SSO, etc for both systems.

### MS Entra Domain Services

- Provides managed domain services such as domain join, group policy, lightweight directory access protocol (LDAP), and Kerberos/NLTM authentication.
    - Managed here means Azure maintains the underlying infrastructure of the service.
    - The following services are related to the management and security of Windows based networks.
        - **Domain join** establishes a computer’s membership in a network domain
            - Example: Work laptops connecting to the main GT domain for example to login to various VMs and servers.
        - **Group policy** ensures consistent configuration across all machines
        - **LDAP** facilitates directory (where user, group and resource information are stored) services
        - **Kerberos/NLTM authentication** verifies the identity of user accessing network resources.
- Allows you to run legacy applications that can’t use modern authentication methods.
- Integrates with MS Entra tenant where a user with MS Entra ID can login and access the services connected to that tenant domain.

**How does it work?**

- By creating a unique name that will act as the domain name.
    
- Two Windows Server domain controllers, also known as a replica set, is deployed into the selected Azure region.
    
- Azure fully manages these domain controllers as well as other physical infrastructure under these services.
    
- Managed domain is configured to perform a one-way synchronization from Entra ID to Entra Domain Services.
    
    - Entra ID does not know the resources created in the managed domain.
    - In hybrid (on-prem + Azure) environment, there is a sync relay that is happening, first from Entra Connect to Entra ID, then from Entra ID to managed domain.
- Azure resources connected in the managed domain can use the features mentioned above.
    

---

## Azure authentication methods

- Multifactor authentication
- SSO

### Passwordless authentication

- Needs to be setup on a machine before it can work.
    
- Usually involves:
    
    - A machine that you need to register to Azure so that Azure knows that the machine belongs to you.
    - Provide a PIN or a fingerprint to prove that this is who you are.
- Different passwordless authentication options that integrate with Microsoft Entra ID:
    
    - **Windows Hello for Business**
        - Ideal for workers that have their own Windows PC.
        - Biometric information or PIN is directly tied to the user through the machine.
        - Has built in support for SSO.
    - **MS Authenticator App**
        - Mobile app that aids with passwordless authentication and multifactor authentication
        - User just need to match the number notified from Authenticator and encode that number to the login page, and using their fingerprint or PIN to confirm.
    - **FIDO2 security keys**
        - Fast Identity Online Alliance helps promote open authentication standards and reduce the use of passwords as form of authentication
        - Latest Standard from FIDO that incorporates Web Authentication standard.
        - Uses an external security key or a platform key built into a device for authentication, typically a USB device, Bluetooth or NFC.

---

## Azure external identities

- External identity is a user, device or service outside of your organization.
- The service that deals with external identities, Microsoft Entra External ID, gives you options on how to interact with external identities.
    - This means controlling how internal users can access external organizations and managing customer’s identity experiences if you’re a developer of a public facing website.
- External users can bring their own login into your application, whether it be credentials from Google/Facebook/their own internal authentication system.
    - This can be achieved by using MS Entra ID or Azure AD B2C to control their access to the organization’s resources.

### Features

- **B2B Collaboration**. External users can use their preferred identity to sign in to MS or enterprise applications.
- **B2B Direct Connect.** Establish a two way mutual trust with another MS Entra organizations.
    - Supports shared Teams channel and enabling access for external users to internal resources.
- **MS Azure AD B2C.** Created Saas apps are published to consumers with Azure AD B2C as identity and access management.
- Can be combined depending on the interactions planned for external organizations and resource types needed to be shared.

---

## Azure conditional access

- Conditional Access is the tool that Entra ID uses to allow/deny access to resources depending on if the user is properly identified, if the user is in the right location, or what device the user is using.
- Offers a more refined multifactor authentication experience.
    - A challenge for second authentication is not triggered if the user is on a known location.
    - Vice versa
- Conditional access will trigger identity signals depending on:
    - The location of the user
    - The deviced used
    - The applications being accessed by user

### When to use?

- Require MFA on required personnel with a specific role in the org.
- Require access to services through approved client applications.
    - Example: Limit email applications that can access your email service.
- Require user to access your application only through trusted devices.
- Block access from untrusted sources.

---

## Role-based access control

- Should follow the Principle of least privilege.
    - Grant access only up to a level needed to complete a task.
- Azure provides built-in roles that contain common access rules for cloud resources.
- You can also define custom rules with custom access rules.
- RBAC is applied to a scope, whether a specific resource or resource group, a subscription or a management group.
- RBAC is hierarchical, meaning if you grant access to the parent scope, then all the child scope will also be affected by those rules.
- Hierarchy:
    - Management group → Subscription → Resource group → Resources

### How is RBAC enforced?

- Resource manager is responsible for enforcing RBAC to any actions that is made on an Azure resource.
- RBAC is not enforced on application or data level.
- RBAC uses an allow model.
    - When you’re assigned a role, RBAC allows you to perform actions within the scope of that role.

---

## Zero trust model

- A security model that assumes the worst case scenario and protects resources based on that assumption.
- Follows the following principles:
    - **Verify explicitly** - Always authenticate and authorize on all available data points.
    - **Use least privilege access** - Use Just In Time (JIT) or Just Enough Access (JEA) risk-based adaptive policies and data protection.
    - **Assume breach** - Minimize blast radius and segment access. Verify end-to-end encryption. Use analytics to get visibility, drive threat detection and improve defenses.
- Traditionally, corporate networks assume that every device connected in their network are safe since only managed devices can enter the network and personal devices are restricted.
    - In this security model, that assumption is ignored. All devices, managed or unmanaged, must be authenticated and access will be given based on the authentication.

---

## Defense in depth

- Its objective is to protect data and prevent others from stealing that data.
- You can visualize defense in depth as a set of layers with the data being the bottom of the layer as it is the entity that is being protected and the other upper layers that act as shield for data.

## Layers

- **Physical security**
    - First line of defense
    - Data center building security and restricting access to the computer hardware
    - Loss or theft of computer hardware is handled.
- **Identity and access**
    - Ensures that identities are secure, access is granted to only what’s needed, and sign-in events and changes are logged.
    - Important to:
        - Control access to infrastructure and change control
        - Use SSO and MFA
        - Audit events and changes
- **Perimeter**
    - Network perimeter that protects from network-based attacks
    - Important to:
        - Use DDoS protection to filter large scale attacks
        - Use perimeter firewalls to identify and alert on malicious attacks
- **Network**
    - Limiting network connectivity across all resources to what’s only required.
    - Important to:
        - Limit communication between resources
        - Deny by default
        - Restrict inbound internet access and limit outbound access when appropriate
        - Implement secure connectivity to on-prem networks.
- **Compute**
    - Compute resources (software) are secured with proper controls in software
    - Important to:
        - Secure access to VM
        - Implement endpoint protection on devices and keep systems patched and current
- **Application**
    - Integrating security on an application’s development to reduce the number of vulnerabilities introduced in code.
    - Important to:
        - Store application secrets in a secure storage medium
        - Make security a design requirement at start of application development.
- **Data**
    - The one responsible for data are the ones who store and control access data.
    - Regulatory requirements dictate the controls and processes of protecting data.
    - Attackers are after data:
        - Stored in database
        - Stored on disk inside virtual machines
        - Stored in SaaS, Office 365
        - Managed through cloud storage

---

## Microsoft Defender for Cloud

- Tool used to monitor cloud, on-prem, hybrid or multi-cloud environment to provide guidance and notifications aimed to strengthen an organization’s security posture - or ability to be ready for any attacks that may be raised.
- Natively integrated to Azure.
    - When environment also involves on-prem or other cloud providers, it will not be able to give the full picture of the status of your organization’s virtual security.
    - MS Defender will deploy Log Analytics agent to gather security related data on environments outside Azure.

### Azure-native protections

- MS Defender helps you detect threats across:
    - **Azure PaaS services -** Azure App Service, SQL, Storage Account.
        - Perform anomaly detection on activity logs.
    - **Azure data services** - Has capabilities to:
        - Automatically classify your data
        - Get assessments for potential vulnerabilities across SQL and Storage services and how to prevent them.
    - **Networks** - Help limit exposures to brute force attacks
        - Reduce access to VM ports by using JIT VM access to prevent unnecessary access.
        - Set secure access policies on selected ports, only for authorized users, allowed source IP address ranges.

### Defend hybrid resources

- Get customized reports and alerts on specific environments.
- Deploy Azure Arc and enable Defender for Cloud to extend protection to on-prem machines.

### Defend resources on other clouds

- Defender for Cloud’s CSPM (Cloud security posture management) features extend to AWS resources.
    - Assesses AWS resources according to AWS specific security recommendations.
    - Resources are also assessed for compliance to AWS specific standards
    - There is a cloud asset inventory page that can help you manage AWS resources alongside Azure resources.
- MS Defender for Containers extends container threat detection and advanced defenses to Amazon EKS Linux clusters.
- MS Defender for Servers brings threat detection and advanced defenses to Windows and Linux EC2 instances.

### Assess, Secure and Defend

- Defender for Cloud fills three vital needs in managing your resources and data:
    - **Assess** - Know your threat readiness and identify and track vulnerabilities
        - Includes automatic and native integration to MS Defender for Endpoint which gives you access to vulnerability findings from MS threat and vulnerability management.
    - **Secure** - Harded resources and services with Azure Security Benchmark
        - Provides continuous monitoring of your resources and services as it scales upward
            - It checks whether the newly provisioned resources satisfies best security practices
            - If they don’t satisfy security requirements, they are flagged and you get recommendations on how to adjust your resource’s configuration to comply with said requirements.
        - A score is assigned based on a resource’s security configuration which gives you a surface level indicator of your security posture.
    - **Defend** - Detect and resolve threats to resources, workloads by providing security alerts and notifications.
        - Security alerts can be used in conjunction with external security provider.
        - Has advanced threat production features such as securing management ports of VMs with just in time access, and adaptive application controls to give/deny access to certain applications.