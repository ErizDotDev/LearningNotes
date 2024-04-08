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
	- This means that ingestion sampling does not affect the accuracy of the metrics collected on the pre-aggregates, regardless of the SDK version used.[w]()