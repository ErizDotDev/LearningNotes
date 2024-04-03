# 🕋 Storage services
---

[[storage-services#Storage account|Storage account]]
[[storage-services#Storage redundancy|Storage redundancy]]
	[[storage-services#Primary region redundancy|Primary region redundancy]]
	[[storage-services#Redundancy in secondary region|Redundancy in secondary region]]
	[[storage-services#Read access to data in secondary region|Read access to data in secondary region]]
[[storage-services#Storage services|Storage services]]
	[[storage-services#Benefits|Benefits]]
	[[storage-services#Azure Blobs|Azure Blobs]]
	[[storage-services#Azure Files|Azure Files]]
	[[storage-services#Azure Queues|Azure Queues]]
	[[storage-services#Azure Disks|Azure Disks]]
	[[storage-services#Azure Tables|Azure Tables]]
[[storage-services#Identify Azure data migration options|Identify Azure data migration options]]
	[[storage-services#Azure Migrate|Azure Migrate]]
[[storage-services#Azure Data Box|Azure Data Box]]
[[storage-services#Identify Azure file movement options|Identify Azure file movement options]]
	[[storage-services#AzCopy|AzCopy]]
	[[storage-services#Azure Storage Explorer|Azure Storage Explorer]]
	[[storage-services#Azure File Sync|Azure File Sync]]

## Storage account

- Provides a unique alias for the data stored in Azure storage which is accessible via HTTP or HTTPS.
- To achieve having a unique namespace for a user’s Azure data, every storage account must have a unique in Azure account name.
    - The combination of account name and the Azure storage service endpoint forms the endpoint of your storage account.
- Rules in creating a unique account name:
    - Can only have 3 to 24 character comprising of lowercase alphabetical character and numbers
    - Name must be unique in the entirety of Azure.

---

## Storage redundancy

- Always stores multiple copies of a user’s data to protect it from planned and unplanned events such as transient hardware failures, network or power outages, etc.
- Consider the tradeoffs between lower costs and high availability when thinking about which redundancy option is suited to your needs along with the following other factors:
    - How data is replicated in the primary region.
    - Whether the data is replicated in a second region that is far from the primary region to protect against regional disasters.
    - Whether your application needs to have read access to the secondary region data if the primary region data goes down.

### Primary region redundancy

- Azure replicates your data three times in the primary region.

**Locally redundant storage (LRS)**

- Replicates your data three times within a single data center in the primary region.
- Provides at least 11 9s (99.999999999%) of durability of objects over a given year.
- Lowest cost redundancy option but also offers the lower durability compared to the other options.
- Protects data against server rack and drive failures.
- If a natural disaster occurs taking out the data center where your file lives, your data will become irrecoverable.
    - To mitigate the risk, other redundancy options are recommended.

**Zone redundant storage (ZRS)**

- For regions where AZs are available, ZRS replicates your data synchronously across three AZs in the primary region.
- Offers durability of 12 9s.
- If a zone becomes unavailable, Azure is responsible for networking updates such as DNS repointing.
    - No manual intervention is needed from the client’s side.
- Recommended for high availability scenarios as well as restricting replication of data to another country or region to meet data governance standards.

### Redundancy in secondary region

- Promotes high data durability.
- Protects your data in case a catastrophic failure is encountered within the primary region of your data.
- The secondary region is based on the Azure region pairs and cannot be updated.
- By default, read and write access isn’t available for read or write access unless there’s a failover to the secondary region in case the primary region encounters failure.
    - After failover has completed, secondary region becomes the primary region and read/write access to data is given.

**Geo-redundant storage (GRS)**

- Similar to LRS with the addition of replicating the data once to a single physical location of your secondary region using LRS.
- Offers 16 9s of durability.

**Geo-zone-redundant storage (GZRS)**

- Similar to ZRS, with the addition of replicating the data to a secondary geographic region, using LRS.
- Recommended for applications that require maximum consistency, durability, and availability, excellent performance, and resilience for disaster recovery.
- Provides at least 16 9s of durability.

### Read access to data in secondary region

- GRS/GZRS can be configured to have read access available to the secondary region, even when the primary region is working flawlessly.
- Enable read access to GRS (RA-GRS) and GZRS (RA-GZRS)

---

## Storage services

### Benefits

- Durable and highly available
- Secure
- Scalable
- Managed
    - Developers do not think about managing disks.
- Accessible

### Azure Blobs

- The Azure version of AWS S3.
- Can store massive amounts of unstructured data, such as text or binary data.
    - Structured data are data that is stored in a logical structure ie. a database table, or a JSON file.
- No restrictions on the kind of data that it can hold.
- Can manage thousands of simultaneous uploads, hold massive amount of video data, constantly growing log files.
- Data is stored as blobs and Azure takes care of where they are stored physically.
- Ideal for:
    - Serving images/documents directly to a browser
    - Storing files for distributed access
    - Streaming audio or video
    - Storing data for backup and restore/archiving
    - Storing data for analysis by on-premises or Azure hosted service.

**Storage tiers**

- Data stored in cloud can grow at an exponential pace.
- To manage costs, data should be organized depending on how frequent they are accessed and how long they are going to be retained in Azure.
- Data access differ on a case to case basis.
    - Some data is actively accessed and modified throughout its lifetime
    - Some data is accessed frequently in the early stages but access drops off as the file gets older
    - Some data is not accessed at all.
- Available access tiers:
    - _Hot access tier:_ Optimized for data that is accessed frequently. (For example, website images)
    - _Cool access tier:_ Optimized for data that is infrequently accessed and stored for at least 30 days. (Example: customer invoices)
    - _Cold access tier_: For data that is also infrequently access and stored for at least 90 days.
    - _Archive access tier_: For data that are rarely accessed and stored for at least 180 days (Example: long-term backups)
- Considerations:
    - Hot and cool access tiers can be set at account level. Cold and archive tiers are not accessible in the account level.
    - The four access tiers can be applied at blob level but only after upload.
    - Data in cool and cold tiers can tolerate low availability but is still highly available.
        - This means cool and cold tiers will charge a fee for frequent access to data and requires a low availability SLA.
    - Archive tier offers lower cost and stores data offline, but will a charge a hefty fee when data is to be accessed.

### Azure Files

- Azure’s version of AWS Elastic File System.
- Manage fully managed file shares in the cloud which are accessible via Server Message Block (SMB) or Network File System (NFS) protocols.
- Allows cloud or on-premises services to access your data.
- SMB Azure file share can be accessed from Windows, Linux, and MacOS while NFS file share is only accessible to Linux and MacOS.
- SMB Azure file shares can be cached in Windows servers with Azure file sync so that data can be easily accessed wherever it is needed.
- Benefits:
    - **Shared access.** Can seamlessly replace on-premises file shares with Azure file shares without worrying about application compatibility.
    - **Fully managed.**
    - **Available scripting and tooling.**
    - **Resiliency.**
    - **Familiar programmability.** Applications running in Azure can access these file shares through System I/O APIs together with Azure SDK APIs for Storage clients.

### Azure Queues

- Service for storing large number of messages, with sizes up to 64KB.
- Queues are commonly used to create backlog of work that will be processed asynchronously.
- Queue storage can be combined with Azure functions to trigger an action when a message is received.

### Azure Disks

- Also known as Azure managed disks, are block-level storage volumes managed by Azure for use with Azure VMs.
- Similar to physical disk, just virtualized.
- Offers greater resiliency and availability than physical disk.
- Azure’s version of AWS Elastic Block Store.

### Azure Tables

- Stores large amount of structured data.
- NoSQL database hosted in Azure that accepts authenticated calls from inside or outside Azure.’

---

## Identify Azure data migration options

- Supports real-time migration of infrastructure, applications and data using Azure Migrate and asynchronous migration using Azure Data Box.

### Azure Migrate

- Helps migration from on-premises environment to the cloud.
- Acts as a hub to help you manage the assessment and migration of data.
- Provides the following:
    - **Unified migration platform.**
    - **Range of tools.** Includes tools like Azure Migrate: Discovery and Azure Migrate: Server Migration. Azure Migrate also integrates with other Azure services.
    - **Assessment and migration.**

**Integrated Tools**

- _Azure Migrate: Discovery and assessment._ Discover and assess on-premises servers running on popular virtual machine runners and servers in preparation for migration to Azure.
- _Azure Migrate: Server Migration._ Migration tool from on-premises environments to Azure.
- _Data Migration Assistant._ Standalone tool to assess SQL servers.
    - Helps pinpoint potential issues that can block deployment.
    - Identifies helpful features that can benefit you on your database migration.
- _Database Migration Service._ Migrate on-prem databases running SQL Server, Azure SQL database, or SQL managed instances to Azure.
- _Azure App service migration._ Standalone tool to assess on-prem websites, written in .NET and PHP to be migrated to Azure App Service
- _Azure Data Box._ Move large amounts of offline data to Azure.

## Azure Data Box

- Azure’s version of AWS Snowball
- Physical migration service that transfers large amount of data via land transport.
- A proprietary Data Box storage device, enclosed in a rugged case during transport, has a maximum storage capacity of 80 terabytes.
- Workflow
    - Order Azure Data Box service in Azure portal.
    - Once device is received, set it up to connect to your network via Web UI.
    - Once transfer is done, send the device back.
    - Data will be automatically uploaded once MS receives the device.
    - Entire process is tracked in the Data Box service in Azure portal.
    - Device will be wiped clean after the import/export orders.

**Use cases:**

- Ideally suited for transfers of more than 40 TB with little to no network connectivity.
- Transfer can be one-time, periodic or initial bulk transfer followed by data transfers in the network.
- Use cases for data export from Azure
    - _Disaster recovery_
    - _Security requirements_
    - _Migrate back to on-prem or other cloud provider._

---

## Identify Azure file movement options

### AzCopy

- Command line utility tool used to copy blobs or files to or from storage account.
- Used to upload/download/copy/synchronize files between storage accounts.
- Can be configured to work with other cloud providers to facilitate transfer of data between cloud providers.

### Azure Storage Explorer

- Standalone app that provides a GUI to manage files.
- Works on the three major OS and uses AzCopy on the backend.

### Azure File Sync

- Tool that centralizes file shares in Azure Files and keep the flexibility, performance, and compatibility of a Windows file server.
- Once installed on a Windows file server, it will automatically stay bi-directionally synced with your files in Azure.
- Features:
    - Use any protocol available on Windows Server (SMB, NFS, FTPS) to access data locally.
    - Have as many caches as you need around the world.
    - Replace a failed local server
    - Configure cloud tiering - Most frequently accessed files will be stored locally and the non-frequently accessed files will be stored in the cloud until needed.