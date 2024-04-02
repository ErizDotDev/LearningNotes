# ☮ Azure Container services

[[containers#Azure Container Registry|Azure Container Registry]]
	[[containers#Use Cases|Use Cases]]
	[[containers#Different Tiers|Different Tiers]]
	[[containers#Registry storage features|Registry storage features]]
	[[containers#Azure Container Registry Tasks|Azure Container Registry Tasks]]
	[[containers#Dockerfile|Dockerfile]]
[[containers#Azure Container Instances|Azure Container Instances]]
	[[containers#Container groups|Container groups]]
	[[containers#Restart policy|Restart policy]]

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
