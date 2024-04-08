# ☮ Azure Container services
---

[[containers#Azure Container Registry|Azure Container Registry]]
	[[containers#Use Cases|Use Cases]]
	[[containers#Different Tiers|Different Tiers]]
	[[containers#Registry storage features|Registry storage features]]
	[[containers#Azure Container Registry Tasks|Azure Container Registry Tasks]]
	[[containers#Dockerfile|Dockerfile]]
[[containers#Azure Container Instances|Azure Container Instances]]
	[[containers#Container groups|Container groups]]
	[[containers#Restart policy|Restart policy]]
	[[containers#Environment variables|Environment variables]]
	[[containers#Mounting an Azure File share|Mounting an Azure File share]]
[[containers#Azure Container Apps|Azure Container Apps]]
	[[containers#Features|Features]]
	[[containers#Container Apps Environment|Container Apps Environment]]
	[[containers#Microservices and Dapr|Microservices and Dapr]]
	[[containers#Multiple containers setup|Multiple containers setup]]
	[[containers#Limitations|Limitations]]
	[[containers#Authentication and Authorization|Authentication and Authorization]]
	[[containers#Managing revisions and secrets|Managing revisions and secrets]]
	[[containers#Dapr|Dapr]]

## Azure Container Registry

- A private Docker registry service that is based on the open-source Docker Registry 2.0.
- Used to create and maintain Docker registries that will host your private docker images.

- Azure Container Registry Tasks helps build container images in Azure partnered with your existing container development and deployment pipelines.
	- This includes OS and framework patch pipeline configuration to automate it.

- **Supported images and formats**
	- A container image is a read-only snapshot of a Docker compatible container.
	- The repository can include both Windows and Linux images.
	- Supports other related formats as <u>Helm charts</u>, and <u>OCI (Open Container Initiative) image formats</u>.

### Use Cases

- Images can be pulled from the container registry and be deployed across different deployment targets:
	- Scalable orchestration systems that handle multiple containerized application from different host clusters (e.g. Kubernetes, Docker Swarm)
	- Azure services such as Azure Kubernetes Service (AKS), App Service, etc.
- Publish to container registry as part of development workflow.
- Simplify building, processing and automating application images development and deployment from different triggers like when their base images are updated, when a Git repository detects a new commit.

### Different Tiers

- **Basic**
	- Have the same capabilities as the higher tiers but with lower storage and throughput.
	- Perfect for beginners who is trying to explore and learn the service and for low usage workflows.
- **Standard**
	- Offers the same capabilities as Basic but with higher throughput and storage.
	- For most production scenarios.
- **Premium**
	- Has the highest storage and concurrent operations for high-volume scenarios.
	- Provides additional features:
		- *Geo-replication*: Manage a registry across different regions.
		- *Content Trust*: For image tag signing.
		- *Private Link*: Provides private access to the registry.

### Registry storage features

- **Encryption at rest** 
	- An image is encrypted before storing it and decrypts the image on the fly when pulled from registry.
- **Regional storage**
	- The registry is hosted on the region where it was created to help customers comply to data registry and compliance requirements.
	- In all regions except Brazil South and SEA, Azure may store registry data in paired regions.
		- Registry data stored in Brazil South and SEA are always confined in the same region to comply with data residency requirements.
	- This feature is to improve the availability of the private Docker images.
	- This option is only available when the geo-replication feature is enabled.
- **Zone redundancy**
	- Only for Premium tier.
	- Replicates your registry data in at least three different AZs on enabled regions.
- **Scalable storage**
	- Can create as many containers, images or tags until the storage limit.
	- The higher the number of container or images hosted in the registry, the more it affects the performance of the registry.
		- It is advised that unused resources are periodically deleted and removed from the repository.
		- Do cleanup with caution as deleted images or containers cannot be recovered after deletion.

### Azure Container Registry Tasks

- Supports various scenarios to build and maintain container images and other artifacts.
- **Quick Task**
	- Build and push a single container to the registry on-demand without local Docker installation.
	- Similar to `docker build` and `docker push` in the cloud.
	- It operates like passing the responsibility of building a container image to Azure.
- **Trigger task on source code update**
- **Trigger task on base image update**
	- Can be configured to also detect image pushes on the public repo like Docker Hub.
- **Schedule a task**
	- Tasks can be configured to do an action based on a timer.
	- Useful for running container workflows on a defined schedule.
		- Example: Running maintenance operations or tests.
- **Multi-step tasks**
	- Definitions of build and push configurations for different container images or artifacts are found in a YAML file.
	- Can also define the steps being automated in a container workflow.
	- Sample workflow:
		- Build a web application image
		- Run the web application container
		- Build a web application test image
		- Run the web application test container, which will perform the test against the running web application container.
		- If the tests pass, a Helm chart archive package will be built.
		- Perform a `helm upgrade` command using the newly created Helm chart archive package.

- By default, ACR Tasks builds images for Linux and `amd64` architecture.
- You can specify the target OS and architecture with the `--platform` option.
- For ARM architectures, specify the variant in OS/architecture/variant format.
	- To specify the OS + architecture combo, follow the same format above but without the variant variable.

### Dockerfile

- Script that contains instructions on how to build a Docker image.
- Contains the following information:
	- Parent image used to create the new image.
	- Commands to update the OS and install software/packages.
	- Build included artifacts such as a developed application. (eg. Keycloak implementation)
	- Services to be exposed, eg. network and storage configuration
	- Commands to run when the container

---

## Azure Container Instances

- The fastest and simplest way to run a container in Azure without maintaining a virtual machine, which is the most common option when hosting a container in the cloud.
- Best for scenarios that can operate in isolated containers such as simple application, task automation and build jobs.

- **Benefits:**
	- *Fast Startup*
	- *Container Access*. Expose your container groups to the internet with a public IP and fully qualified domain name. (FQDN)
	- *Hypervisor-level security*. Container is isolated like when it is hosted in a VM.
	- *Minimal customer data footprint*. Container runs with minimal customer data required.
	- *Custom hardware specifications*. 
		- Allows exact specifications of CPU cores and memory that are required.
		- Azure will allocate resources in the container group depending on the requested specifications one container needs for its instance.
	- *Persistent storage*. Mount Azure Files shares directly to a container for persisting and retrieving of data.
	- *Features are available for both Linux and Windows*. 

### Container groups

- Top level resource in ACI that hosts containers within the same host machine.
- Containers in a container group share the same storage volume, network configuration, lifecycle and resources.
- Multi-container groups is only available in Linux.
	- Windows containers only support single instances.

- The best use case for multi-container groups is dividing a single functional task into multiple container instances. Examples include:
	- An application container and a logging container
		- The logging container will collate all logs and metrics gathered from the application and persist them in long term storage.
	- An application container and a monitoring container
		- A monitoring container periodically makes a request to the application to check its health and ensure that it is running and responding.
	- Front-end container and back-end container.

- **Deployment**
	- **Two ways of deploying a multi-container group.
		- *[[managing-deploying-resources#ARM template|Resource Manager template]]
			- Use this only when there are other services that need to be provisioned together with the container like a Azure Files share.
		- *YAML file*
			- Recommended for provisioning of container instances ONLY.

- **Networking**
	- **A container group share an IP address and a port namespace on that IP address.
			- A port namespace allows each container instance to listen to the same port number without conflict.
		- The ports will come in handy for communication purposes between each container instances and other services both in public and private environments.

- **Storage**
	- **The following storage medium can be mounted on the container group.
		- Azure Files share
		- Cloned git repository
		- Empty directory
		- Secret

### Restart policy

- Because of how easy and fast the ACI makes it to build and deploy container instances, it allowed for the utilization of run-once tasks like building, testing and deploying container instances.
- ACIs are charged by the second, which makes it important to have a restart policy mechanism enabled to protect the user from incurring changes against underutilized container instances that are turned on 24/7.
- The restart policy allows a container instance to be turned off after it completed its workload.

- **Available restart policy options**
	- `Always` - Default value.
	- `Never` - Container instance will run at most once.
	- `OnFailure` - Container instance will run at least once.

- When Azure stops an container, either because the application inside the container is set up this way or because of failure, and the restart policy of the specified is `Never` or `OnFailure`, the container status will then become `Terminated`.

### Environment variables

- Environment variables are used to provide dynamic configuration to your application that is running in the container.
- They are passed to the docker container via script or command during the container creation.
- To create a container with environment variables: ^4de26a

```bash
az container create \
	--resource-group <resource_group_name> \
	--name <container_name> \
	--image <image_url> \
	--restart-policy OnFailure \
	--environment-variables 'NumWords'='5' 'MinLength'='8'
```

- Notice that the environment variables come in the following key value, space-delimited format: `'NumWords'='5' 'MinLength'='8'`

- It is possible to add secure values as an environment variable.
	- A secure value holds important information like passwords and keys.
	- Secure values, except for its name, won't be displayed on the Portal UI.
	- In the YAML file, replace `value` property with `secureValue` to define an environment variable with secure value.
	- [[commands#^f887f8|To create a container instance with a YAML file]]

### Mounting an Azure File share

- Azure Container Instances are stateless by default.
	- If the container crashes or stops, all of its state is lost.

- To persist the state during the lifetime of its container and beyond, an external store, [[storage-services#Azure Files|Azure Files]] share for instance, should be mounted to it.

- **Limitations**
	- Azure Files can only be mounted on Linux containers.
	- The Linux container should be run as `root` if Azure File share is going to be mounted.
	- Only supports CIFS (Common Internet File System) protocol for file sharing.

- When [[commands#^db3187|mounting an Azure Files store]], ensure that the `--dns-name-label` is unique within the region where the container instance is located and created.
	- Update this value when you receive a **DNS name label** error message.

- It is better to deploy a container group with a mounted Azure Files using the YAML approach.

- When deploying and mounting multiple file stores, aside from YAML file, the developer can prepare an ARM template.
	- The ARM template should contain the following sections:
		- `volumes`
			- This is where the file share configurations take place.
			- Accepts an array.
		- `volumeMounts`:
			- Part of the container group's section.
			- In the mount path, ensure that the `shareName` defined in the `volumes` section is being used.

---

## Azure Container Apps

[[compute-networking-services#Azure Container Apps|See initial information here]]

- Enables the execution of microservices and containerized applications on a serverless platform that runs on top of Azure Kubernetes Service.

- Takes care of the Kubernetes and container orchestration details.
	- Restarts the container if it crashes.

- Supports any Linux-based x86-64 container image.
	- No base image needed.

- Common use cases:
	- Deploy API endpoints
	- Host background processing/long-running tasks.
	- Run microservices
	- Handle event-driven processes

- Can dynamically scale based on:
	- HTTP traffic
	- event-driven processing
	- CPU/memory load

### Features

- Run multiple container versions and manage their respective container app's application lifecycle.
- Split traffic across multiple containers representing multiple application versions used for Blue/Green testing or A/B testing.
- Use CLI, Portal or ARM templates to manage container applications.
- Virtual network is provided in the environment created for container apps.
- Directly manage secrets in your application.
- Log monitoring using Azure Log Analytics.
- Secured public access can be enabled in your container applications without worrying about other infrastructure or components by enabling the HTTPS ingress feature in your container application.
- Enable internal ingress coupled with a DNS-based service discovery for secure internal-only endpoints.

- Autoscaling based on KEDA-Supported scaler triggers.
	- KEDA (Kubernetes Event-Driven Autoscaling) provides event-driven autoscaling for workloads running in Kubernetes.
		- Scale triggers in Azure include Azure Queue message length, or custom metrics generated by the application.
	- Scaling based on CPU or memory load won't scale applications down to 0.

- Can be integrated with Dapr to create microservice applications.
	- Dapr (Distributed application runtime) is a runtime that simplifies the process of building microservices and distributed applications.

- Run containers from any registry, public or private, including Docker Hub, and ACR.
	- Container registries should be defined in the configuration by providing that registry's credentials.
		- The password is defined in another section and the `passwordSecretRef` will call the name instead of the value.

### Container Apps Environment

- A series of microservice applications are deployed in a single Container Apps environment, which serves as a boundary around a group of container apps.
	- These applications are hosted in the same virtual network and logs are consolidated in the same Log Analytics workspace.
	- An existing virtual network can be used.

- Deploying applications in the same container app environment is useful for hosting related applications either through Dapr, Log Analytics, or resources that they may be using and if applications need to be in the same virtual network.
- Consider deploying applications in different container app environments when applications won't use the same resource and Dapr's communication service API is not used.

### Microservices and Dapr

- Azure Container Apps can be a tool for developing applications that follow the microservice architecture, with features that align with the core principles of microservice application development:
	- Independent scaling, versioning and upgrades
		- Microservice architecture is known for having components or core functionalities that can independently be versioned, upgraded and scaled.
	- Service discovery
	- Dapr integration

- Microservices tend to call different calls distributed across the network between components.
	- It is important that the applications stay resilient as there is a tendency of having a lot of pain points in this type of architecture.
	- Dapr helps with this by providing observability, pub/sub implementation, and a custom mechanism for service to service communication using TLS, retries, etc.

### Multiple containers setup

- It is possible to have multiple containers in a single container app to implement the sidecar pattern.
	- The containers will share the same network resource and hard disk.
	- The containers will also have the same lifecycle.
	- A sidecar pattern is basically attaching a core service (logging, caching mechanism) to the main application in a single container.
		- This is an advanced use case.
			- It is always recommended to have services separated in their own containers.

### Limitations

- Azure Container Apps cannot run applications that run processes that require root access.
- Only Linux images are allowed for use.

### Authentication and Authorization

- Container Apps have a built in authorization and authentication flow to secure your publicly accessible, external ingress enabled applications with little to no code required.
	- Feature is only available for HTTPS.
	- `allowInsecure` property in your ingress configuration should be disabled.

- Your Container Apps application can restrict access only to authenticated users or allow unauthenticated access to anyone using you application.

- The authentication and authorization component is a middleware application that sits sidecar (sidebar container) on your application.
	- Every incoming HTTP request goes through the security middleware first before being handled by the application.

- Have similar [[app-service#^c584a1|authentication features]] and [[app-service#Auth flow|auth flow]] as App Service.
- The authentication and authorization module is found on a separate container different from where your application is hosted.

### Managing revisions and secrets

- Revisions are immutable container app versions.
	- They are created when the application is updated with "revision scope changes".
	- Used for releasing a new version of your app or rolling back to a previous version of your application quickly.

- You can set which revisions are active, and the traffic that will go through each active revision.

- A revision name can be used to identify a revision and specifying it is as simple as updating the revision suffix.
	- If a container app has a name of `album-api`, a suffix, in this case `v1` can be added to specify a revision.
		- Now the revision name becomes `album-api--v1` which specifies that this is the first version of the container app.
	- The suffix can be specified in the ARM template or when creating the container app via the CLI.

- To list revisions of a container app: ^393094

```bash
az containerapp revision list \
	--name <container_app_name> \
	--resource-group <resource_group_name> \
	-o table
```

- Azure Container Apps also protects sensitive information that powers your application, its secrets.
	- Secrets are available to an application, regardless of what revision it's in.
	- Adding removing or changing secrets will not generate a new revision.
	- An application can reference one or more secrets
	- Multiple applications can reference the same secret.

- To register a new secret or updates to your secret, do the following:
	- Deploy a new revision
	- Restart an existing revision.

- Before deleting a secret, make sure to deploy a revision that does not reference the old secret, then deactivate all revisions that uses the old secret.

- Secrets can be referenced as an environment variable in a revision once registered.
	- Secret name is used when referencing a secret in an environment variable.
		- To use a secret in an environment variable in the configuration, set its value using `[[commands|secretref:]]`

### Dapr

**How it works?**
- Dapr is enabled by setting up the container app configuration to enable Dapr and providing the requisite properties and values.
	- The configuration will be applied to all container app revisions running individually or all at the same time.
- The Dapr APIs are available for query via HTTP (port 3500) or gRPC (port 50001) as they are integrated in your application as sidebar containers.
- Dapr setup will require your app's functionalities to be configured in a component like structure.
	- These components/functionalities will be available for all container apps deployed, single or multiple.
	- Components are treated as dependencies where only the required components specified in a scopes array will be loaded in runtime.
		- Dependencies can be loaded for various applications/services or can be setup so that they can only be used by a specific application/service.

- Dapr is configured for enablement via CLI, ARM templates, Bicep and the Azure portal.
- Dapr secrets can be utilized to securely fetch configuration metadata.