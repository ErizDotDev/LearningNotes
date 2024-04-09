# ☮ Application Insights

## Overview

- Provides application performance monitoring (APM) tooling.
- Free of use UNTIL the application grew to a substantial size.

- APM tools monitor application during development, testing and in Production by helping people:
	- Understand how an application performs.
	- Identify RCA of an issue by reviewing data during application run when the issue occurred.

### High-level features
 
- Collects application metrics and telemetry data pertaining to app activity and health
- Collects application trace logs.
	- Trace logs are additional logs that gives a detailed view of an activity that happened within the application.
		- Enabling trace logs is as simple as providing a log location for it.
		- Very rare to update logging framework.

### Detailed features:

- Observes application activity in real-time without affecting host environment.
- Injects requests occasionally to the app's external endpoints to test application's availability and responsiveness in random times.
- Integrates with Github or ADO
	- Automatically creates ADO work items based on data collected.
- Gives insight as to which application features are used by your users and how the users interact and use your system.
- Catches failures and anomalies automatically by consistently analyzing telemetry data.
- Provides a high-level, deconstructed, top-down view of the application architecture and status of each application components.
- Visualize a flow of an execution or transaction end-to-end.

### What data is Application Insights monitoring?

- **Page request rate including response times and failure rates**
	- Which pages are most popular and at what times of day?
	- Where your users are located?
	- Which pages perform best?
- **Service dependency metrics**
	- Which services slow the application down.
- **Exceptions**
	- Analyze common exceptions being thrown.
	- Check a specific exception at a specific time with all relevant details attached like stack trace.
	- Both server and browser caught exceptions are collected.
- **Page views and load performance**
- How the application responds to AJAX calls and how it affects application performance.
- User and session counts
- Virtual machine/container performance counters like CPU, memory and network usage.
- Infrastructure metrics from Docker and Azure
	- Pertains to similar data monitored on the previous point.
- Custom events and metrics emitted by the application.

### Getting started

- Application Insights can be added in the application code for custom application telemetry, allowing it to send more data and logs that is custom only to the application.
- Can also be setup during an application's runtime.
	- This does not require a code change unlike the previous point.
	- Includes hooking up the application in server and client-side with ways to collect relevant telemetry data from each aspect of the application.
- Ping your application from Azure to check their availability.

---

## Log-based metrics

### Overview

- Log-based metrics are collated from existing logs data and can be converted to **Kusto queries** as the data to be extracted can be customized to a specific need.
	- *Kusto query* is a query language used for extracting specific data from logs with its syntax closely resembling both SQL and [[query-expression|LINQ's query expression]].
	- Log-based metrics are more catered towards detail-driven, ad-hoc data analysis and application diagnosis.

- On the other hand, *standard metrics* are data that are collected in a standard time range and covers common data point like performance metrics. 
	- Has better query performance
	- Suitable choice for real-time alerting and dashboards.
### Purposes

- Allows for the analyzation of your app's health over time.
- Dashboard creation
- Alerts configuration

### Log data collection

- Application Insight will collect all these data from whatever means it was generated and display its visualization in Azure Portal for further analysis.
- Most of the time, the abundance of details being logged is helpful in identifying certain aspects of application like resolving issues and detailed checking of application health and usage.
- Other times, the level of detail introduces bloat in logs and makes them impractical to be kept.
	- Because of this, Application Insights have specific features that can help trim down the logs through **volume reduction techniques** like sampling and filtering.
		- Using these techniques decreases the accuracy of the metrics.

### Pre-aggregated metrics

- Only supported on the most recent version of the SDK (v.2.7+)
	- For older versions, pre-aggregated metrics can still be used for improved query performance.
		- What is missing is the data transmission speed.

- Stored as a set of numbers representing the value of a specific metric in a given time frame (minute/second/hour) with a small set of metadata that describes the specific instance that metric is collected (eg: web page URL, browser used, visitor's geographic location, etc.) 
	- This format gives a significant boost in query performance which leads to more robust features like near real-time alerting, responsive dashboards, etc.

- NOTE: The endpoint that collects the events and the logs will pre-aggregate these data first before ingestion sampling, one of the log data volume reduction techniques.
	- This means that ingestion sampling does not affect the accuracy of the metrics collected on the pre-aggregates, regardless of the SDK version used.

### App instrumentation for monitoring

- Instrumentation refers to use of measuring instruments to measure and quantify specific metrics and statistics.

- Auto-instrumentation is the default and preferred way of attaching measuring devices to applications where source code is not available.
	- No need to think about SDK updates and code changes.
	- To enable, just configure the agent to automatically collect telemetry data 

- The SDK should only be used when:
	- Introducing custom events and metrics
	- Requiring complete control of the telemetry data flow
	- Auto-instrumentation is not available because of language or platform limitations.
- It is not required to have the app and its components deployed in Azure.
	- The application with the SDK installed can connect to Application Insights using a token.

- The SDK, on available languages, support distributed tracing.
	- Distributed tracing is a technique that checks the flow of the request through various services and components of a distributed system.
		- This allows for the analyzation of how services interact with one another in a distributed architecture.

- Can integrate with OpenCensus, which is an open-source set of library used to provide metric collection and distributed tracing capabilities.
	- Allows for easy integration with Redis, MongoDB, Memcached through open source contributions.

### Availability Test

- Used to check your application's responsiveness and availability after its deployment.
- Availability tests are available for use on any HTTP or HTTPS endpoints whether its your own application or not.
	- Used for testing endpoints that your services rely on.

- Can create up to 100 availability tests per Application Insights resource.

- Types of availability tests:
	- **URL Ping test**
		- Can set various success criteria with advanced features like:
			- Requests parsing
			- Allowing for retries
		- Relies on public DNS infrastructure to resolve domain names.
			- If application is using private DNS, ensure that it can resolve public DNS names in your test.
			- If not possible, use `TrackAvailability` tests instead.

	- **Standard test**
		- Tests include SSL validity, lifetime checks which are done without manual intervention, HTTP request verb, custom headers and custom data associated with an HTTP request.

	- Custom `TrackAvailability()` tests (via SDK) where data collected is sent to Application Insights
	- **Multi-step test**
		- This test is only available on VS 2019.
		- `TrackAvailability()` tests are the most widely used option for multi request and authentication scenarios.

### Application Map

- Gives a visual representation of your system and its components where you can check for any performance bottlenecks or spots where failures most likely happens.
	- Each component will represent a node in the map and each node can provide further information about its health, performance and alert metrics which includes Application Insights events.
		- Components may be set up such that it has a different instrumentation key or registered in  different Application Insights resource but they will still appear in the map as part of the system's components.

- The application map generates the visual guide by following HTTP calls made between servers and resources where Application Insights is installed.
	- This operation may take some time depending on the complexity of the application.
		- This operation won't take long if components are registered in the same Application Insights resource.

