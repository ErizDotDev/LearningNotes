# Blob Storage

Overview
	[[blob-storage#Types of storage accounts|Types of storage accounts]]
	[[blob-storage#Resource types|Resource types]]
	[[blob-storage#Security features|Security features]]
	[[blob-storage#Static web hosting in Azure Storage|Static web hosting in Azure storage]]
[[blob-storage#Lifecycle|Lifecycle]]
	[[blob-storage#Access tier features|Access tier features]]
	[[blob-storage#Data lifecycle management|Data lifecycle management]]
	[[blob-storage#Lifecycle Policies|Lifecycle Policies]]
	[[blob-storage#Rehydrate data from archive tier|Rehydrate data from archive tier]]
[[blob-storage#Working with the SDK|Working with the SDK]]
	[[blob-storage#Metadata|Metadata]]

[[storage-services#Azure Blobs|Initial information]]

- Users or client applications can access stored blob objects wherever they are in the world via HTTP/HTTPS.
- Objects can be accessed programmatically using Azure Storage REST API, Powershell, CLI or the SDK library.
- Azure Storage account is the top-level container of the Blob storage.
    - Azure Storage account provides the namespace for your Azure Storage data that is accessible from anywhere in the world.
    - The default endpoint is derived from the account name for the storage account plus the Blob storage default URL.

### Types of storage accounts

- Offers two different performance levels of storage accounts, each having its own features and pricing model.
    
    - **Standard** - General purpose account and is recommended for most use-cases;
    - **Premium** - Data are stored in SSD meaning better I/O performance.
- General classification of storage accounts
    
    - **General-purpose v2** - Recommended for most use-cases of file storage in Azure
    - **Block blob**
        - Typically used by workflows where they need to access files quickly, frequently and in a performant manner with low latency speeds.
        - Premium storage account type for block blobs (text, binary data) and append blobs (logs, any other data or file that is constantly being appended to)
    - **Page blob**
        - Page blob involves random read and write operations and is common in scenarios like the storage acting as a virtual machine disk storage or databases randomly reading or writing through the storage at any given time.
        - Random access file can be as large as 8TB in size.
        - Page blobs store files in virtual hard drives.
- The default access tier is the Hot tier.
    
- One thing to remember about access tier is that the more basic the plan is, the cheaper the access cost to that storage access and the higher the storage cost.
    
    - As we move up the access tiers, the more expensive the access cost becomes and the storage costs get cheaper.

### Resource types

- Three resource types in Blob storage:
    
    - Storage account
    - Storage account container
    - Blob in the storage account container
- **Container**
    
    - Organizes blobs in a file system like structure.
    - A storage account can contain multiple containers and a container can contain multiple number of blobs.
    - The container name shall be a valid DNS name and should satisfy name requirements because this will be included in the blob endpoint path when accessing a specific blob.
        - The container name must be:
            - > 65 characters long
                
            - Alphanumeric characters and dashes
            - Double dashes are not allowed.
- **Blobs**
    
    - Supports block, append and page blobs.

### Security features

- All data written in Azure Storage are automatically encrypted using SSE (Storage Service Encryption)
    
- Has native support for Microsoft Entra ID and RBAC.
    
    - Assign RBAC roles to storage account with the help of Microsoft Entra ID to authorize resource management operations such as key management.
        - RBAC roles can be scoped from the storage account level, to the subscription level, resource group, or container level.
        - Microsoft Entra ID supports blob and queue data operations.
- Data transfer from an application to Azure is secured using HTTPS, and client-side encyrption.
    
- Azure Disk encryptions can help encrypt the drives and the OS used by Azure virtual machines.
    
- Grant external users temporary access to data in Azure Storage using shared access signature.
    
- Encryption used is compliant to most organization’s security compliance requirements.
    
- Data sitting in Azure Storage is encrypted and decrypted transparently using strong encryptions.
    
    - Uses 256-bit AES encryption, one of the strongest block cipher available.
- The encryption is enabled by default regardless of the pricing and performance tier the storage account is on and cannot ever be disabled.
    
    - No code is needed to enable file encryption to Azure.
- Encrypted resources in Azure storage include:
    
    - All files and containers
    - Copies of containers
    - Storage account
    - Object metadata
- Encryption is free of cost Azure feature and does not affect Azure Storage performance.
    
- To encrypt your storage accounts, you can use Microsoft-managed keys, or use your own keys which Azure provides you with two options:
    
    - Specify a **customer-managed key** to encrypt and decrypt all data in the storage account.
    - Specify a **customer-provided key** in which the user will provide the key to gain read and write access to a blob stored in storage.
        - This provides fine-tuned control on which user accesses what file and a directive on how certain are encrypted/decrypted.
        - Has limited availability on data access compared to the customer-managed key.

### Static web hosting in Azure Storage

- You can host static websites in Azure storage similar to AWS S3.
    
    - Website assets are stored in the $web container.
- This enables the use of serverless architectures that includes Azure Functions, etc.
    
- Great budget option if a use of web server is not required.
    
- One of the major limitations of serving static websites this way is the lack of HTTP header configuration options.
    
    - The best way to this is through the use of Azure CDN.
    - To allow configuration of HTTP headers, it’s better to use **Azure Static Web Apps**.
- The $web container’s public access level can be configured but it won’t affect the public access to the endpoint used to access the website.
    
    - The website is public, has anonymous and readonly access to the website files being served.
    - The access control configuration defines the access of the $web container to the public.
        - The $web path can be used when requesting specific website files.
- Disabling the public access of the storage account itself won’t affect the static websites that are hosted in Azure Storage.
    
- Azure Storage natively supports custom domain for your hosted static websites.
    
- To use HTTPS, you have to use Azure CDN.
    

---

## Lifecycle

### Access tier features

- Hot and cool tiers apply to all redundancy options. (LRS, GRS, etc.)
- Archive tier only applies to LRS, GRS and RA-GRS.
- Storage limit is applied to an account level, not on the access tier level.
    - Storage limit can be implemented to one or all of your access tiers available.

### Data lifecycle management

- Azure Blob storage has a lifecycle management feature that allows the user to define rules and policies on how their data is going to transition from one access tier to another, up until expiration.
    - This policy is available for General-purpose v2 and Blob storage accounts.
- **Features:**
    - Transition blobs to cooler tiers (hot to cool, cold to cold, etc.) to optimize performance and cost.
        - The longer a blob stays in the hot tier the more expensive it is to keep it is there.
        - It is important to transition so that storage expense can be managed
    - Delete blobs at the end of their lifecycle.
    - Define the rules that will be ran once a day to implement the life cycle policies.
    - Rules can be applied to containers or specific blobs (using prefixes)
- **NOTE:** Data stored in premium block storage is not tiered and must be transferred first into a container in the Hot tier via Put Block from URL API.
    - Put Block will copy the data on the server and its request will only be completed once the data is successfully transferred to the destination location.

### Lifecycle Policies

- A policy is a JSON file, that can be created as a file or as a series of inputs in Azure portal, containing a filter set and an action set:
    
    - The _filter set_ defines the list of objects or blobs that will be hit with the lifecycle **action.
    - The *action set *****defines an action or a tier to the objects filtered by the filter set.
- A policy should contain one or more rules, up to 100.
    
- **Rule parameters**
    
    - _`name` -_ Name can have up to 256 alphanumeric characters
    - _`enabled` -_ Default value is `true`. Set if a rule is actively applied or not.
    - _`type` -_ Current valid type is `Lifecycle`
    - _`definition` -_ Contains the filter set and action set.
- **Filter set**
    
    - Sample
    
    ```json
    "filters": {
        "blobTypes": [ "blockBlob" ],
        "prefixMatch": [ "container1/foo" ]
      }
    ```
    
    - **Properties**
        - _`blobTypes`_
            - The only required property in filter set definition.
            - Type of blob objects that will be filtered.
                - Can be block or append.
        - _`prefixMatch`_
            - Prefix pattern that will be used to catch all the objects that will be the target of the action set.
            - Should include a container in the definition.
            - Can contain up to 10 prefixes.
        - _`blobIndexMatch` -_ An array of key value pairs that defines the index key as well as the condition that will be validated to check if an object should be placed in the filter list.
- **Action set:**
    
    - Sample
    
    ```json
    "actions": {
        "baseBlob": {
          "tierToCool": { "daysAfterModificationGreaterThan": 30 },
          "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
          "delete": { "daysAfterModificationGreaterThan": 2555 }
        },
        "snapshot": {
          "delete": { "daysAfterCreationGreaterThan": 90 }
        }
      }
    ```
    
    - At least one of these actions should be applied:
        - _`tierToCool`_
        - _`enableAutoTierToHotFromCool`_
        - _`tierToArchive`_
        - _`delete`_
    - Most of the actions above are only applied to block blobs.
        - The `delete` action can be applied to append blobs as well.
    - The actions above are sorted from the least cheapest to the cheapest action.
        - If there is a conflict in the action set that was created, the system will use the cheapest action defined.
    - The run conditions that are placed inside an action is based on age:
        - Run conditions can count days after modification date, creation date, last access time, and last tier change.
        - Base blobs use the last modification date while blob snapshots use the snapshot creation date for age tracking.
- A lifecycle policy can be created using the common data management tools like the Powershell, CLI, portal and REST APIs of Azure.
    

### Rehydrate data from archive tier

- To access data that is in the archive tier, which are in offline mode, it needs to undergo an rehydration process where the archive tier data must be transferred first into an online container, either in the Hot or Cool tier.
- You can do the above:
    - By simple copying via the Copy Blob or Copy Blob from URL operations.
        - The two operations above are recommended actions by MS.
        - The blob being copied must be stored under a different name or in a different container.
        - The copy operation can only be done within the same storage account.
        - The copy cannot be done on the same archive tier, only on online tiers is this operation allowed.
    - Change a blob’s access tier to an online tier using the Set Blob Tier operation.
        - Once this option has started, it can’t be cancelled.
- The above operations will take some time.
- Rehydrating large number of blobs all at the same time is more efficient than rehydrating small number of blobs concurrently.
- **IMPORTANT:**
    - Changing a blob’s tier access tier does not affect its last modified date.
    - If there is a lifecycle management policy in place, a scenario can happen where a blob has been rehydrated to an online tier but is sent back to the archive because of age tracking that may depend on the last modified date.

**Priority**

- Rehydration priority can be set via the `x-ms-hydrate-priority` header in the copy or set blob tier operations.
- **Available priorities:**
    - _Standard -_ Priority will be processed as received and can take up to 15 hours.
    - _High -_ Takes priority over Standard and can be completed in under an hour for objects less than 10 GB in size.

---

## Working with the SDK

- To work with Blob storage using the SDK, a client object must be created first.
    - A client object is a class that allows you to access varying levels of Azure Storage objects in the hierarchy (storage accounts → containers → blobs).
- There are different client objects for the different Azure Storage Objects.
    - `BlobServiceClient`
        - Gives CRUD capabilities to making containers inside storage containers as well as configure storage account properties.
        - This will also help access containers for updating.
        - To initialize, you can manually provide the storage account URL in the constructor as well as the credential method.
            - The client object use `DefaultAzureCredential` by default.
            - The storage account URL can be constructed manually or can be retrieved in runtime using another SDK method.
    - `BlobContainerClient`
        - Gives CRUD capabilities to specific containers and the blobs inside them.
        - Depends on `BlobServiceClient` to generate this container client object, using the `GetBlobContainerClient` method.
        - If working on a single specific container, the client object can be initialized the same way as the `BlobServiceClient`
    - `BlobClient`
        - Provides CRUD operations for specific blob objects.
- How does the client object get authenticated and authorized to perform its operations?
    - Obtains an access token for authorization by providing secrets and keys coming from the application supplied in a configuration.
    - The access token is used as a credential when the client object is initialized, and this credential will persist for the entire lifetime of the client object.
    - Microsoft security principal requesting the token must have the correct RBAC role to be grant access to the storage data.

### Metadata

- Blob containers support system properties and user-defined metadata
    - _System properties_ are your typical file properties found in our file systems e.g. Last Modified Date, Blob Tier, Access Tier, Content Type
    - _User defined metadata_ adds more information about an object without affecting the object’s behavior within the system.
- Both these information can be set as HTTP header, thus their values should be valid HTTP header values and must follow value specifications.