# ☮ Azure Container services

[[containers#Azure Container Registry|Azure Container Registry]]
	[[containers#Use Cases|Use Cases]]
	[[containers#Different Tiers|Different Tiers]]
	[[containers#Registry storage features|Registry storage features]]
	[[containers#Azure Container Registry Tasks|Azure Container Registry Tasks]]

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

