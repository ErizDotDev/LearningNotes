# ☮ App Service
---

Overview
	[[app-service#Limitations|Limitations]]
[[app-service#Service plans|Service plans]]
	[[app-service#Pricing plans|Pricing plans]]
	[[app-service#Usage strategy|Usage strategy]]
[[app-service#Deployment|Deployment]]
	[[app-service#Automated|Automated]]
	[[app-service#Manual|Manual]]
	[[app-service#Deployment slots|Deployment slots]]
[[app-service#Authentication and authorization|Authentication and authorization]]
	[[app-service#How it works?|How it works?]]
	[[app-service#Auth flow|Auth flow]]
	[[app-service#Authorization behavior|Authorization behavior]]
[[app-service#Networking features|Networking features]]
	[[app-service#Multi-tenant networking features|Multi-tenant networking features]]
	[[app-service#Default networking behavior|Default networking behavior]]
[[app-service#App Settings|App Settings]]
	[[app-service#General Settings|General Settings]]
	[[app-service#Path mappings|Path mappings]]
[[app-service#Enable logging|Enable logging]]
	[[app-service#Adding log messages in Code|Adding log messages in Code]]
[[app-service#Configuring security certificates|Configuring security certificates]]
	[[app-service#Available options|Available options]]
[[app-service#Auto scale factors|Auto scale factors]]
	[[app-service#When to use?|When to use?]]
	[[app-service#Autoscaling and App Service Pricing plans|Autoscaling and App Service Pricing plans]]
	[[app-service#Metrics used for autoscaling rules|Metrics used for autoscaling rules]]
	[[app-service#Setting up autoscale|Setting up autoscale]]
	[[app-service#Best practices|Best practices]]
[[app-service#Deployment slots|Deployment slots]]
	[[app-service#Auto swap|Auto swap]]
	[[app-service#Custom warm-ups|Custom warm-ups]]
	[[app-service#Traffic routing using deployment slots|Traffic routing using deployment slots]]

- App Service has a separate deployment slot that users can utilize for their staging or test environments.
    - This comes in extra as there is a default deployment slot for production.
- Deployment slots have their own host name.
- Deployment slots are located on the same VM instance as the default production slot
- You can choose to run App Service in Linux with all supported tech stacks.
- If the application does not conform with any of the available stacks for Linux use, upload a custom container for it.
    - Code, assets and application content are stored in Azure storage in a storage volume.

### Limitations

- App Service hosted in Linux is not available in the Sharing/Basic tier.
- Not every feature of Linux hosting is available in Azure portal.
- Disk latency is higher and more varied on built-in App Service images for Linux as opposed to custom images;
    - Use custom image if there is a need for high-speed read latency for files and assets in the application;

### Commands

- To list down all supported runtimes for App Service applications:

```bash
az webapp list-runtimes
```

- Use the `os-type` option to specify a specific OS for target runtimes:

```bash
az webapp list-runtimes --os-type linux
```

---

## Service plans

- A service plan contains a list of resources required to run an app.
    - One or more plans can be included in a service plan
- A service is defined by the following:
    - Region
    - Number of VM instance
    - Size of VM instance
    - Pricing plan
    - OS
- Service plan also holds the scaling plan for the applications.
    - Choose to run the app in 5 VMs simultaneously or setup the app for autoscaling.

### Pricing plans

- **Shared compute**
    - Available variants: Free and shared
    - Multiple clients share the same Azure VM to run their respective apps
    - CPU resources are allocated for each app and they can’t be scaled out.
        - CPU minutes is the resource being allocated to the application, which refers to the amount of CPU time used to execute the process.
- **Dedicated compute**
    - Basic, Standard, Premium, Premiumv2, Premiumv3
    - Run apps on dedicated Azure VMs in regular Azure App Service environment.
    - Apps in the same App Service plan will share the same compute resources.
    - Higher the tier, the higher the VM instance can be used for scaling the application.
- **Isolated**
    - Runs dedicated VMs running in Azure virtual network for specific user to host the app.
    - Provides network and compute isolation for your apps.
    - Provides the maximum scale-out option.

### Usage strategy

- To enable scaling of your application to the desired number of instances, or infrastructure, simply change the service plan of your application.
- Savings can be increased if multiple apps can be hosted in a single service plan.
    - Ensure the capacity of the new app to be added in the plan.
        - There might be a chance that the underlying infrastructure cannot support an additional application.
- Isolate your application to a new Service plan when:
    - App is resource intensive
    - Scale the app independently from other applications
    - App needs resource in a different geographical region

---

## Deployment

### Automated

- Continuous deployment
- Push out features, changes, and fixes in a repetitive pattern with a minimal impact to the end users.
- Supports automated deployment from the following sources: Azure DevOps services, Github, BitBucket

### Manual

- Via Git: App Service web apps provide a Git remote URL where developers can push their changes to deploy the app;
- Via Azure CLI: `az webapp up` command packages your code and deploys it and if a web app is not yet created it will create one.
- Zip deploy: Use `curl` or other HTTP utility tool to send a ZIP of the application to Azure App Service
- FTP or through SFTP.

### Deployment slots

- Use deployment slot to deploy a new production build
- If the service plan is using the Standard plan, you can deploy the app to a staging environment and then swap the staging and production slots.
    - This will minimize downtime as this process will warm up the worker processes to match the production environment, so by the time the switch is executed, it will seem like the switch did not happen and the performance has not suffered.

---

## Authentication and authorization

- Provides built-in authentication and authorization support, allowing users to sign in and access data with writing minimal.
    
    - It is not required for your web app to use this feature as you may choose to use the built-in security features of the web framework you’re using.
- Allows the implementation of multiple login providers like Microsoft Entra ID, Facebook, Google, Twitter, etc.
    
- Uses federated identity - a third party provides the auth flow and manages the user identities on your behalf.
    
    - Additional identity providers include any OpenID Connect provider and Github
- A token store, where all the tokens used by users for the application, API is being stored.
    
    - Automatically enabled together with enabling authentication on your app in App Service.
- If application logging is enabled, authentication and authorization logs are part of the log files.
    

### How it works?

- The authentication and authorization modules are located in the same environment as your application code.
- When enabled, all HTTP requests will pass first through these auth modules before being transferred to your app code.
- The module runs independently from your app code and can be configured using a config file or via Azure Resource Manager settings.
- No other configuration in the application is needed.
- In Linux, the module is run in a separate container which achieves a similar effect as that to Windows OS variant.
- Performs the following:
    - Manages authenticated sessions
    - Validate, generate and refresh OAuth tokens
    - Authenticates users with available identity providers
    - Injects identity information (auth tokens) into HTTP request headers

### Auth flow

- Auth flow has two variations: One where the login is directed to Microsoft Entra (server-directed) or the login is directed to the selected third party identity provider (client-directed)
- Client-directed auth flow is characterized by using third party providers’ SDK to authenticate their users.
    - Typically, these SDK will expose methods to do the authentication for the users.
    - This auth flow does not use redirect mechanisms.
        - Instead, the SDK will be able to obtain the auth token that will be used to authenticate and authorize subsequent requests.
    - Requires additional step of providing the generated auth token by client to the provider URL to be validated.
- Server-directed auth flow is characterized by the app code manually accessing identity provider URLs to generate auth tokens, then passing them to the App Service to be used for subsequent requests.
    - Uses redirect mechanisms to go through various authentication URLs to obtain tokens.

### Authorization behavior

- Control what happens to unauthenticated requests.
    - **Allow unauthenticated requests**
        - App code will handle the unauthenticated requests and Azure will provide auth information via the request header.
        - Allows an application to provide multiple sign-in providers to users.
    - **Require authentication**
        - Will reject unauthenticated requests
        - Configure behavior after rejecting the unauthenticated requests.
            - Example: Redirect user to provider login, or send out 401 or 403 response back to user.

---

## Networking features

- By default, apps hosted in App Service are publicly accessible and they can only access public endpoints in the internet with no additional configuration needed.
    - They cannot access resources in private addresses even if they are also hosted in Azure without additional network configuration.

### Multi-tenant networking features

- Azure is a multi-tenant cloud provider, meaning multiple customers share the same network infrastructure.
    - Connecting Azure App Service network to a specific private network is impossible since many customers share the same network infra, we can’t connect underlying App Service network to a single network for a specific customer or client.
- What is possible is controlling traffic that comes in and out of your application.

### Default networking behavior

- Free and Shared plans run on multitenant workers sharing the same resources
- The Dedicated compute plan variants run on environments dedicated to that one specific App Service plan.
    - All applications hosted in a single Standard plan run in the same worker that can be scaled based on demand, by spawning new workers.

**Outbound IP addresses**
- All the Dedicated compute plan variants except the Premium v2+ variants (Premium v2 and Premium v3) all share the same worker VM.
- Premium v2 and v3 have their own worker VMs each with their own performance upgrades compared to the previous.
- Changing VM families (or plan variants) will also update the outbound IP addresses that is available for use.
    - Each VM family (or plan variant) has their own assigned IP addresses that they can utilize.
    - All apps under that plan all share the same outbound IP addresses.
- Available for viewing on the Properties tab on the App Service portal interface.

### Commands

- To list down all the outbound IP addresses used by your App Service application:

```bash
az webapp show \
	--resource-group <group_name> \
	--name <app_name> \
	--query outboundIpAddresses \
	--output tsv
```

- To find all possible outbound IP addresses for your app:

```bash
az webapp show \
	az webapp show \
	--resource-group <group_name> \
	--name <app_name> \
	--query possibleOutboundIpAddresses \
	--output tsv
```

---

## App Settings

- Environment variables that live inside your app.
    
    - In Linux, these environment variables are passed into the container through the `--env` flag.
- Similar to app settings found in .NET Core applications and web dot config files in .NET Framework.
    
    - The values in App Settings will override whatever the value is written in the application’s configuration files.
        - Best to keep the values in App Service with the test or local development environment values in the application’s configuration files.
- The environment variable values stored in App Settings are all encrypted.
    
- There is a separate page or section for updating connection strings and follow the same underlying principle as the app setting value insertion or update.
    
    - Useful for .NET application development
    - For other stacks, use app settings section exclusively for connection strings
    - Connection strings are also encrypted
- In Linux, any setting formatted as `SettingSection:SettingProp1` should be formatted as `SettingSection__SettingProp1`
    

### General Settings

- **Stack.** The tech stack the application uses
- **Bitness.** 32 or 64 bit
- **WebSocket protocol.** To enable use of [ASP.NET](http://ASP.NET) SignalR or [socket.io](http://socket.io)
- **Always on.** Keep the app online even without incoming traffic.
    - If this setting is turned off, app will be unloaded after 20 minutes of no traffic.
    - This setting should be on for WebJobs or WebJobs triggered with CRON time expression.
- **Managed pipeline version.** Dictates the IIS version to use.
    - Set to _Classic_ if the app is legacy and requires an older version of IIS.
    - **HTTP version.** Set to 2.0 to enable support for HTTP2
- **Session affinity.** In a multi-instance deployment, it ensures that requests are routed to the same instance during its lifetime.
    - Turn off this setting for stateless applications, like REST APIs.
- **Remote debugging.** Enable remote debugging for .NET, .NET Core and NodeJS apps.
    - This setting is turned off automatically after 48 hours.
- A setting is also available that sets the minimum TLS encryption version to provide additional security to the requests that is trying to access the app.

### Path mappings

- This is where you set IIS handler mappings, virtual applications and directories.
    - **Handler mappings** allows you to define a custom script processor that will handle requests for a specific file extension.
        - Common example of this is HTML `StaticFileModule` and ASPX files’ `aspNetCore` script processors that process their respective files.
- For Linux, this page is allotted for provisioning additional custom storage resource.

---

## Enable logging

- App Service provides different logs to help developers trace and debug errors occurring in their applications.
- Most of the logs are stored in Azure Storage blobs if not on the App Service file system itself.
- Most logs are only available to Windows images.
    - Only application and deployment logs are available to Linux images.
- Log types:
    - **Application logs.** Logs generated by the application code.
    - **Web server logs.** Contains raw HTTP request data containing network information from the source.
    - **Detailed error logging.** HTML pages of the error messages intended to be returned to the users, even if these error messages are not actually shown to the users because it is not standard practice.
        - App Service saves these HTML pages and logs them.
    - **Failed request tracing.** Detailed trace of failed request including the logs from IIS processing said request.
    - **Deployment logging.** Helps trace why a deployment failed.
        - Happens automatically and cannot be configured.

### Adding log messages in Code

- In .NET Framework apps, logs can be created using `System.Diagnostics.Trace.Debug()`
    - Other log variants are available like `Error`, `Info`, `Warning`
- For .NET Core apps, it is recommended the logging package `Microsoft.Extensions.Logging.AppServices`

---

## Configuring security certificates

- App Service offers various tools that help you setup, create, or import security certificates to help protect data transfer between you App Service applications and your clients.
- Providing your own custom private certificates is allowed but should follow certain requirements.

### Available options

- Create a free App Service managed certificate.
    - A private certificate that is free of charge used to secure your own custom domain.
    - Only available for Dedicated and Isolated pricing plan variants.
    - Managed by App Service and renewed continuously and automatically.
    - Is limited in feature sets which includes:
        - Not supporting wildcard characters.
        - Is not exportable
        - Is not supported in App Service Environment.
- Purchase an App Service certificate.
    - Azure monitored private certificate.
    - Offers automated certificate management and renewal and export options.
- Import certificate from Key Vault.
- Upload a private certificate.
- Upload a public certificate.

---

## Auto scale factors

- Autoscaling can be triggered by schedule, by assessing the resources if are starting to deplete, or a combination of both.
    
    - An example scenario of resources that starts to deplete is when CPU utilization is peaking, machine memory is growing, or surging number of HTTP requests.
    - Combining a metric and schedule based rules for autoscaling ensures that autoscaling is done incrementally.
        - Example: If the number of awaiting requests per minute is greater than 1000 from 8am to 5pm, increase the application instance by 1.
        - The opposite of this is setting a hard count on the number of instances that are going to be provisioned which is inefficient.
- Azure App Service constantly checks resource metrics while the app is running.
    
    - It determines what resources are needed to handle the increasing workload and provisions these resources before the entire application starts to overload.
    - App Service checks these stats and will add or remove web servers based on demand.
- Autoscaling is based on user-defined rules.
    
    - Rules are where the user specifies which metric should be monitored, what condition needs to be met and what action is needed to be taken (scale the app out or in)
        - An event will be triggered once the limit or threshold is reached.
    - Multiple rules can be created.
    - A default rule is used if none of the rules defined are applicable.
        - This rule is always available and has no schedule.
- Autoscaling should be configured correctly and carefully (pragmatic approach).
    
    - In an event of a DDoS attack, it would not be worth the effort to try processing each requests.
        - A better solution for this is to implement a filter that will weed out all fake requests.

### When to use?

- Autoscaling improves an application’s availability and fault tolerance.
- Autoscaling will not be able to help on long-running, performance-intensive tasks.
    - A request can only be handled by a single application instance.
        - Chances are processing a request with lots of complex bits can cause performance issues and could exhaust the memory and processing capacity of the application instance.
    - In this case, manually scaling your application instance to use a more powerful processor for example, would be a better solution.
- Autoscaling will also not be able to help on handling long-term growth.
    - As the number of users of an application grow, it will be hard to predict the scaling numbers would be.
    - If the growth numbers can be predicted, manual scaling will be the more cost-effective approach.
- The starting number of instances of application can be a limitation as well.
    - If an organization is used to running a few instances at the start, the application may suffer from performance issues as App Service will try to spin up more instances which can take time due to lack of capacity.

### Autoscaling and App Service Pricing plans

- Auto scaling is tied to App Service pricing plans.
    - Based on the pricing tier your app is in, Azure imposes a limit of instances an app can scale to.
        - The higher the tier, the higher the number of instances allowed.
    - Not all pricing tiers support autoscaling.

### Metrics used for autoscaling rules

- CPU and memory percentage (separate)
- Disk queue length
    - The number of I/O requests pending across all instances
    - The higher the number, the higher the chance of disk contention occurring.
        - Disk contention means multiple processes are competing for disk storage resources causing bottlenecks in disk operations.
- HTTP Queue Length
    - App may return 408 status if many requests are waiting to be processed.
- Data In and Out. (separate)
- Can also rely on metrics from other Azure services (example: Number of active messages in Service Bus)

### How autoscaling rules analyze metrics?

- A two-step process.
- Autoscale rule will collect all metric data across all instances for a period of time known as _time grain._
    - An autoscale rule can collect most metrics for as long as 1 minute of time grain.
    - The value collected, processed, and summarized from this period of time is known as _time aggregation_ and aggregation functions such as Min, Max, Sum, Last and Count can be called on it.
- Autoscale rule will process and calculate data derived from the time aggregation into a longer period of time.
    - 1 minute may be too short of a period to derive any meaningful decision of whether to scale an application in or out.
        - So the data must be tested on a longer period of time (a _duration_) to make an effective measurement.
- The value derived from calculating an aggregate from a duration can be different from the aggregate derived from a time grain.
    - Comparing short term vs. long term data proves that there will be a variation in data.

### Setting up autoscale

- By default, App Service only implements manual scaling.
    - To push through with creating your own custom scaling conditions, choose **Custom autoscale**
- First, add an autoscale condition.
    - You can either scale based on a metric or based on a hard number of instance that you allow.
    - When scaling based on metric, you need to define the limits (as many as your pricing plan will allow) and the schedule when this autoscaling condition should run.
- After creating an autoscale condition, an autoscale rule will need to be created.
    - This will determine when the autoscaling action will be executed.
    - This is where the user will specify the metrics that will act as basis for the rule, the specific data that will be monitored and the limit for that specific metric.
    - An autoscale condition can have one or more rules defined.
- A feature is available where the user can track all instances when autoscaling occurred, the number of instances that was spun up and the conditions that triggered the autoscaling action.
    - Use the data shown in the Run History feature above with other charts shown in the Overview page to give you insight as to how the autoscaling is linked with other resources and their respective utilizations.

### Best practices

- Ensure that there are gaps in the value of the minimum and maximum number of instances.
    - This is to ensure that autoscaling action can be triggered and will be noticeable.
- Always choose the appropriate metric for your scaling rules.
    - Commonly used time aggregation function is Average.
- Consider carefully the metric thresholds that will be used for the autoscale rules.
    - There is a field now that accepts a value that determines the number of minutes it will take to scale out or scale in again.
        - This is to combat the issue on the value of limits being crucial in determining the scaling mechanism of your application.
    - Make sure that there are gaps between the metric limits when scaling in or scaling out instances.
    - Scenarios can happen when the inappropriate metric is used and there is not much margin assigned to the threshold limits, the autoscale will fall in an infinite loop.
        - If the monitored metric threshold reaches to a certain value as calculated behind the scenes by the autoscale engine, it will cause the autoscale to scale in or out, then scale back out or in again because the limit values are just too close enough.
            - The metric can play a factor in it too.
- Take note of the following scaling rules:
    - When scaling out (increasing instances), autoscale will run if any rules are met.
    - When scaling in (decreasing instances), autoscale will only run if ALL rules are met.
- Always select a safe default instance count.
    - This is important because autoscale will hard scale to this number if the metrics are not available.
- Configure autoscale notifications.
    - Useful for scenarios when:
        - Autoscale successfully executes or fails; or
        - Metrics are not available to make an autoscale decision and when the metrics become available again

---

## Deployment slots

- Only available for Dedicated pricing plan variants.
    
- One of the benefits of deploying a production site into a non-production slot is that there is a backup in case things go south with the newly deployed updates.
    
    - You can switch back the last good production copy in the now backup slot back into the main production slot.
- You can only scale up on a different tier if you want to retain/increase the number of deployment slots your application currently has.
    
- App service does the following to ensure that slot swapping does not experience downtime.
    
    - Apply production deployment slots settings to the non-production deployment slots, from connection string, application and deployment settings.
        - This will pause all instances from the non-prod slot and will trigger them to restart.
        - At this point, the swap operation is paused and you are given the opportunity to check that the settings are applied correctly.
    - Wait for all instances to complete their restarts.
        - If a failure is encountered in one of the instances during the restarts, the configurations on the non-prod slots are rollbacked.
        - In the case of failure or the swap taking way too long, there are logs available in the Activity Log under the Swap Web App Slots category where you can check the details of the issue that was encountered during slot swapping.
    - Trigger local cache initialization by doing a request-response test calling the application root.
        - This step will be enacted only if local cache is enabled.
        - Wait until instances complete and receive a response from the test.
        - This event will trigger another restart for the instances.
    - If auto-swap is enabled with custom warm-up, do another request-response test.
        - If an instance returns any HTTP response, it is considered warmed up.
    - If all instances in the non-prod slot has been warmed up, the swap will initiate by switching the routing rules between the prod and non-prod slots.
        - After this step, the warmed-up non-prod slots is now the prod slot.
    - The previous prod slot will then be applied with the settings that is applied initially to the previously non-prod slot and trigger the restarts.

### Auto swap

- Enables your CI/CD process to deploy your app continuously with little to no downtime and minimize cold starts.
- Once auto-swap is enabled, after pushing code changes to the non-prod slot, App Service will automatically swap the slots in production after it has warmed up the non-prod slot.
- Auto-swap is not available in Linux and Web App for containers.

### Custom warm-ups

- Some apps may require custom warm-up process before the swap.
- `applicationInitialization` node in web.config allows you to declare the following properties that is referenced by the App Service during the slot swapping process.
    - URL paths that will be pinged by App Service to warm up the site, and when restarting the instances.
        - Default value is `/` but the ideal value would be the status check API path of your application.
        - Valid HTTP status codes that is expected to be returned by your application when pinged.
            - If the response code is not on the list, the warmup and startup process stops.

### Traffic routing using deployment slots

- By default, all traffic from the URL of your App Service application goes to the production slot.
    
- It can be configured so that some of the traffic can be directed to another slot.
    - This is useful when showcasing a new feature that you want users to give you feedback on, but this feature is not to be released yet in production.

- When you configure to route traffic automatically, you need to specify the number of requests in percentage that will be routed to the staging slot.
    
- There is an available HTTP header, `x-ms-routing-name`, that can inform the user in client browser on what slot they were directed to.
    - If the user has been redirected to the non-production slot, the value of the header will be `x-ms-routing-name=staging`.
        - Otherwise, the value of the header is `x-ms-routing-name=self`.

- When you configure to route traffic manually, you can instead use the header as query parameters that can be appended on your application’s URL.
    - The link can then be used by displaying a link to your application inviting users to view the new feature of your application.
        - This way the user can opt in to view the preview of your application’s new feature in the staging environment, if they want to.

- By default, the percentage value for traffic routing configuration is 0%.
    - If this is the case, even if the users is trying to use the link to go the preview version of the application, they will not be able to access it, because App Service is restricting traffic to the staging slots.
    - This is advanced use case where the staging slot is hidden from the public while an internal team is testing the features on the slot.