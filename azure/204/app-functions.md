# ☮ Azure Functions
---

Overview
	[[app-functions#Difference between Azure Functions and Azure Logic Apps|Difference between Azure Functions and Azure Logic Apps]]
	[[app-functions#Difference between Azure Functions and WebJobs|Difference between Azure Functions and WebJobs]]
[[app-functions#Hosting Options|Hosting Options]]
	[[app-functions#Hosting Plans and Acoustic|Hosting Plans and Acoustic]]
	[[app-functions#Storage account requirements|Storage account requirements]]
[[app-functions#Scaling|Scaling]]
	[[app-functions#Runtime scaling|Runtime scaling]]
	[[app-functions#Scaling behavior|Scaling behavior]]
	[[app-functions#Limit scale-out|Limit scale-out]]
[[app-functions#Development|Development]]
	[[app-functions#Connecting to Azure services|Connecting to Azure services]]

[[compute-networking-services#^ed9e52|Initial information]]

- Azure’s version of AWS Lambda.
- A serverless solution that allows you to write and deploy code without worrying about the underlying infrastructure and network.
- Supports triggers which are ways to kickstart an action defined in your code.
- Also supports bindings which provides a way to reference external resources into your AZ Functions code without the need for additional code to connect to these services or resources.
    - External resources are mostly Azure related services like database table, blob storages, queue, Cosmos DB and many more.

### Difference between Azure Functions and Azure Logic Apps

- Azure Functions is a serverless compute solution while Azure Logic Apps is a serverless workflow integration platform.
- Both features create **orchestrations** or a series of steps in order to complete a complex worklow or action.
    - Orchestrations in Azure Functions are created by code by using the Durable Functions extension.
    - Orchestrations in Azure Logic Apps are created by GUI or config files.
- **Azure Logic Apps**
    - Azure’s version of AWS Step Functions.
    - Can orchestrate a bunch of different services to complete a workflow or a complex and advanced action.

### Difference between Azure Functions and WebJobs

- AZ Functions is built on top of WebJobs SDK so it shares the same triggers and events for other Azure services.
- AZ Functions offer more features and capabilities than WebJobs making it the better choice most of the time.

---

## Hosting Options

- There are the hosting options available.
    - **Consumption plan**
        - Default.
        - Has auto scaling - instances are added or removed depending on the number of incoming events.
        - Only pay for the compute resources when your functions are running.
    - **Premium Plan**
        - Also has auto scaling but uses pre-warmed workers so applications can still run without delay after being idle (resolves cold start issues)
            - A _cold start_ happens because when a Function app has no requests to process, it scales down to 0 instances running.
                - When a request comes in in a state where the number of instances active is 0, it needs to power up an instance which takes some time and this is what causes a cold start.
        - Runs on more powerful instances
        - Can connect to virtual networks.
    - App Service (Dedicated) plan
        - Run applications within App Service using App Service rates.
        - Best used for long running tasks where Durable Functions aren’t applicable.
    - **ASE (App Service Environment)**
        - Provides fully isolated and dedicated environment to run your App Service applications on.
    - **Kubernetes (Direct or Azure Arc)**
        - Run App Service in a Kubernetes platform that is fully isolated and dedicated only to that purpose.

### Hosting Plans and Acoustic

- Most of the auto scale functionality are event-based or based on the number of events that are being received by the application.
- Max number of instances can range from 100-200 for Windows, 20-100 for Linux, and 10-20 instances for dedicated plans.
- Default and max timeout for each hosting plan in minutes.
    - Consumption: 5-10
    - Premium: 30-infinity
    - Dedicated: 30-infinity

### Storage account requirements

- On any plan, AZ function require a regular storage account which supports Azure Blob, Queue, Files and Table storage.
- This storage is where the code of the function app is hosted, where the logs will be stored and how the triggers are managed.
- You may use the same account used by your AZ Functions app so they can be used by the triggers and bindings to your application data.
- If you delete the storage account where the AZ Functions are located, the code and other assets like logs will also be deleted and cannot be recovered.
- For storage-intensive applications, use a separate storage account.

---

## Scaling

- Instances are created/removed based on the number of events that trigger a function.
- Each instance is limited to 1.5 GB of memory and one CPU.
- An instance host the entire function application, which means all functions in the function app share resource within an instance and scale at the same time.
- Multiple AZ Function apps can be hosted in the same hosting plan and each function can scale without affecting the other function apps.

### Runtime scaling

- Uses Scale Controller to monitor the rate of events and determines whether the Function app will be scaled out or scaled in.
- The scale controller will depend on the innate property of the trigger type.
    - Example: An event triggered by a Queue will scale depending on the number of queue messages and the age of the oldest queue message.

### Scaling behavior

- A single instance may process more than one message or request so there is no set limit for number of concurrent executions.
- For HTTP triggers, instances can be created, at most, once per second.
    - For non-HTTP triggers, it can allocate an instance once every 30 seconds.

### Limit scale-out

- You can restrict the number of instances created when a Function app is scaling out.
- This is useful for cases such as a database only allowing a certain number of transactions to be processed per second.
- Update the `functionAppScaleLimit` property.
    - Set its value to 0 or null if limit is not imposed.

---

## Development

- Two components of an Azure Function
    - The code
    - The config file, specifically, _function.json_
        - Defines the function’s trigger, bindings and other configuration settings.
        - Can accept multiple bindings

- Required binding properties in configuration
    - Name of binding
        - Make sure to know the assigned name of a binding or a trigger to be used in this field.
            - Each Azure service has an assigned value.
    - Direction of binding if it will be a request (in) or a response (out)
        - Triggers are always in an `in` direction.
    - Name of the bound data.
        - In C# or Java, this name is the name of the method parameter expected in the function.
        - For output binding, to use the return value of the function, use `$return` as name.
    - Connection strings of specific Azure resource

- Function App code written in C# or Java can forego the configuration file and use decorators or attributes to give information about the trigger or binding that will be used by the function.
- Javascript code mimics similarly to what AWS Lambda code looks like.

- Function application is consolidation of one or more functions.
    - One application, even with multiple functions, will share the same deployment plan, deployment method and runtime version.
    - Required to be in the same language.
    - All functions that are part of an application are inside a specific folder with a host configuration file _host.json_
        - The **host.json** contains runtime specific configuration.
        - The folder structure will also contain the bin folder where the binaries and compiled code is generated.

- Avoid maintaining or updating code in Azure portal.
    - Make all the necessary code and config changes in local environment.

- When an Azure Functions app is created, the following resources are created:
    - The Function app;
    - Storage account where all the assets for the Function app is being stored;
    - Consumption plan - provides serverless execution environment for the functions;
    - Application insights instance - tracks usage of the serverless function;
    - Resource group to contain all of these resources together;

### Connecting to Azure services

- Locally, the connections are made using the connection strings found in the configuration file.
- In Azure, the connection strings are placed in Azure Application Settings as environment variables that are then referenced to the Azure Function.
- By default, the functions are configured to use secrets.
    - Configuring functions using an identity is also an option by using a managed identity.
    - By default, system assigned identity is used.
    - User assigned identity can be used and can be configured with the `credential` and the `clientID` properties.
- These identities must have the right permissions to perform actions in Azure Functions.
    - Permissions are assigned as role in RBAC or through access policy depending on which service are you connecting to.