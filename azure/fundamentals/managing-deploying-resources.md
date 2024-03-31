# 🕋 Managing and deploying resources

[[managing-deploying-resources#Tools interacting with Azure|Tools interacting with Azure]]
	[[managing-deploying-resources#Azure Portal|Azure Portal]]
	[[managing-deploying-resources#Azure Cloud Shell|Azure Cloud Shell]]
	[[managing-deploying-resources#Azure PowerShell|Azure PowerShell]]
	[[managing-deploying-resources#Azure CLI|Azure CLI]]
	[[managing-deploying-resources#Azure Arc|Azure Arc]]
[[managing-deploying-resources#Azure Resource Manager|Azure Resource Manager]]
	[[managing-deploying-resources#ARM template|ARM template]]
	[[managing-deploying-resources#Infrastructure as code|Infrastructure as code]]
	[[managing-deploying-resources#Bicep|Bicep]]

## Tools interacting with Azure

- Azure provides multiple tools to interact with your Azure environment.
    - via Azure portal
    - via PowerShell
    - via CLI

### Azure Portal

- Provides a GUI for users to manage their Azure resources and create dashboards to give them better visibility of their environment.
- Always available and rarely goes on a downtime even with continuous updates.
- Designed to be fast by being in every Azure datacenter so that customers can have close access to it.

### Azure Cloud Shell

- Web-based terminal used to create, manage and configure resources which can be accessed in Azure Portal.
- Shares similar feature set as Azure PowerShell and CLI, and you can choose your usage option between the two.
- Automatically authenticates you as you login to the portal and immediately knows your permissions.
    - Unlike the offline PowerShell and CLI where you have to authenticate first to access your resources.

### Azure PowerShell

- Installed locally on your machine.
- Utilizes custom Azure commands that calls Azure REST APIs to handle creation, management and configuration of your resources.
- Useful for automating complex workflows like setting up suite of resources for a solution using a script or tearing down resource groups with just automated command.

### Azure CLI

- Similar to PowerShell, only this one use the Bash syntax.

---

## Azure Arc

- Useful for bringing Azure’s compliance and governance management and monitoring tools to your multi-cloud or on-prem hybrid environments by utilizing Azure Resource Manager (ARM).
- Features
    - Resources located in on-prem or multi-cloud environments become visible and can be managed in Azure.
    - Use Azure services and management capabilities whether you’re in on-prem or multi-cloud environment.
    - Manages the following resources:
        - Servers
        - Kubernetes clusters
        - Azure data services
        - SQL server
        - Virtual machines

---

## Azure Resource Manager

- Deployment and management service of Azure.
    
- ARM is involved when resources are being created, maintained or deleted.
    
- Request flow:
    
    - Azure PowerShell or CLI makes a request to ARM
    - ARM receives the request
    - ARM authenticates and authorizes the request
    - ARM routes the request to the appropriate service.
- RBAC is natively integrated to ARM so you can apply access control to all services.
    

### ARM template

- A JSON file that is used to define what resources will be deployed in Azure.
- Deployment code is sure to be verified first before it was run, ensuring the integrity and correctness of the resource deployment that is going to take place.
    - Under the hood, it ensures the correct dependencies between resources that it is well connected and configured.
- Helpful in implementing infrastructure as a code paradigm.
- **Benefits**
    - Declare resources that are to be deployed with less programming or computing syntax and flow
    - Allows repeatable deployments with ensured confidence in the integrity and consistency of said deployment.
    - Orchestrates the deployment of individual resources effectively and in parallel, ensuring the correct order in which these resources are being deployed and provisioned.
    - Templates can be broken down into small, well-defined chunks and these chunks can be reused in templates defining an entire environment like Development or Test environments.
    - Azure PowerShell or CLI can be incorporated in templates as scripts that can be referenced as actions to complete during the deployment.

### Infrastructure as code

- Managing infrastructure as lines of code.
- Allows repeatable actions such as deployments and resource configurations.

### Bicep

- Has similar abilities with ARM template but with more simple and concise syntax and style.
- Uses a proprietary Bicep file format.
- **Benefits**
    - Simpler and easy to read than JSON files
        - Helpful for non-programmers
    - Has immediate support for all preview and new versions of Azure services.
    - All the other benefits of ARM templates including orchestration, modularity and repeatable results.