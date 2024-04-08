# ⏲ Commands
---
## Authentication

- Trigger a login to Azure

```bash
az login
```


## CLI Packages and Extensions

- To install a CLI extension
	- In the example, change `containerapp` in any extensions that you wanted to be installed.

```bash
az extension add --name containerapp --upgrade
```

- To install a CLI package
	- A CLI package is like a C# namespace/Nuget package name.

```bash
az provider register --namespace <namespace>
```

## Resource management

- Create a resource group
	- Replace `<region>` with actual Azure region.

```bash
az group create \
	--location <region> \
	--name <resource_group_name>
```

- Delete resource group
	- Useful for removing resource groups during learning/trial period to ensure that any resources won't incur the user any charges.

```bash
az group delete --name <resource_group_name> --no-wait
```

## Blob Storage

- Create a storage account
	- Ensure that the storage account name is unique across the entire Azure.

```bash
az storage account create \
	--resource-group <resource_group_name> \
	--name <storage_account_name> \
	--location <region> \
	--sku Standard_LRS
```

- [[blob-storage#Commands|Create a lifecycle policy for storage account]]

## App Service

- [[app-service#Commands|App Service runtime commands]]
- [[app-service#Networking features#Commands|Fetch network information commands]]

## Azure Container Registry

- To build and push images to the registry.
	- Syntax and available options may be similar to the `docker build` command.

```bash
az acr build
```

## Azure Container Instances

- To create and start a container instance:
	- You can specify a restart policy to attach to the container when it's created.
	- Azure File share resource can also be mounted to the container before it's created and started. ^db3187
	- It is also possible to [[containers#^4de26a|add environment variables]] during the creation of a container instance.

```bash
az container create \
	--resource-group <resource_group_name> \
	--name <container_name> \
	--image <image_url> \
	--ports <port_to_be_exposed> \
	--dns-name-label <dns_name> \
	--location <region>
	--restart-policy OnFailure \ #optional
	--azure-file-volume-account-name <azure_file_volume_account_name> \ #optional
	--azure-file-volume-account-key <azure_file_volume_account_key> \ #optional
	--azure-file-volume-share-name <azure_file_volume_share_name> \ #optional
	--azure-file-volume-mount-path <log_path> #optional
```

- To create a container instance with a YAML file: ^f887f8

```bash
az container create \
	--resource-group <resource_group_name> \
	--file <yaml_file_name>
```

- To show container details and status:

```bash
az container show \
	--resource-group <resource_group_name> \
	--name <container_name> \
	--query "{FQDN:ipaddress.fqdn,ProvisioningState:provisioningState}" \
	--out table
```

## Azure Container Apps

- To create a container app environment:

```bash
az containerapp env create \
	--name <container_app_env_name> \
	--resource-group <resource_group_name> \
	--location <region>
```

- To create a container app:
	- Secrets can be provided during this operation following the `key=value` format.
	- Environment variables can also be set here

```bash
az containerapp create \
	--name <container_app_name> \
	--resource-group <resource_group_name> \
	--environment <container_app_env_name> \
	--image <image_url> \
	--target-port <exposed_port> \
	--ingress 'external' \ # internal or external
	--query properties.configuration.ingress.fqdn
	--secrets "key=value" \ #optional
	--env-vars "ConnectionString=secretref:<secret_name>" #optional
```

- To update a container app:

```bash
az containerapp update \
	--name <container_app_name> \
	--resource-group <resource_group_name> \
	--image <image_url>
```

- [[containers#^393094|To list a container app's revision list.]]
