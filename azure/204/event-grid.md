# ☮️ Azure Event Grid
---

- Azure equivalent of AWS EventBridge.
- A serverless event broker service fully ingrained in Azure and its other services that simplifies the consumption of events by efficiently redirecting events from Azure and non-Azure resources and distributes the events to registered subscriber endpoints.
	- This results in lower costs by removing the need to poll endpoints to get certain responses from services.
	- Useful for applications powered by event-driven architectures and the pub/sub pattern (publisher/subscriber)
		- Publishers can be any Azure services or SaaS applications.

## Concepts in Event Grid

### Events

- What happened in the system.
- Contains generic information such as the event source, the time the event occurred, and an ID as well as information specific to a particular occurrence.
	- Example: A file upload event will give details about the file that was uploaded like its file size, and date last modified.
- Only events with a size of 64KB is covered by SLA.
	- Events exceeding 64KB are charged a fee per additional 64KB.
- Headers can be added to an event, in case a subscription will require them.
	- Can have up to 10 headers.
	- Each header value should have a size of at most 4096 bytes.

### Event Source

- Where the event happens.
- Responsible for sending events to the Event Grid.
- Each event source represents the type of events that occurred in it.
	- Azure Storage is the source for blob related events.
	- IoT Hub is the source for device related events.
	- Your application is the source of custom events.

### Topics

- Provides endpoints where event sources send their events.
- Publisher creates a topic and they can create multiple topics.
- To respond to certain types of events, subscribers must choose which topic they should subscribe to.

- Types of topics:
	- **System topics**
		- Azure service specific topics not visible in your subscription.
		- However, subscribers can choose these topics to subscribe to them for specific events.
	- **Custom topics**. Topics that are generated by the application or third party, and are visible in your subscription.

### Event subscriptions

- Tells Event Grid which events in the topic you're interested in receiving.
- Creating a subscription is creating an endpoint that will handle the event.
	- Events can be filtered out through this endpoint by their event type or subject pattern.
- Expiration can be configured for subscriptions.
	- Useful for short lived subscriptions as it will automatically clean up said expired subscription.

### Event handlers

- Where the event is sent.
- Provides additional steps that will further process the event.
- Supports several handler types such as an Azure service or a custom webhook that you created.
	- Depending on the selected handler type, Event Grid will follow the handler's specific process to guarantee the delivery of the event.
		- For webhook, event will be retried until it returns the status code of 200.
		- For Azure Storage Queue, event will be retried until the service successfully pushes the message into the queue.

## Event schemas

- Can be an Event Grid event schema or a Cloud event schema.
	- Can be used interchangeably;
	- Allows back and forth conversion;
- Events have a set of properties that is common on any publisher.
	- The data object contains information specific to a publisher.
		- For system topics, the properties are specific to the Azure resource or service.

- Event sources are sending collections of events to Event Grid as an array.
	- The array should be 1MB at most.
	- If the size of the array exceeds the 1MB limit, they will get a `413 Payload Too Large` response.

### Event Grid schema

- Contains properties like `topic`, `subject`, `eventType`, `id`, `data` and many more.
- Custom topics should contain the standard properties.
	- Their data properties can contain their own custom properties.

- When publishing events to custom topics, create subjects.
	- Subjects are used to filter and distribute events.
	- Subject should have a path value that will represent where the event took place.
		- Paths are effective in narrowing or widening filters that can be used to identify events in a topic.
	- Use subjects like how you filter files in a directory tree.
		- You can also choose to filter out by file extension as it is possible.

### Cloud schema

- Azure natively supports the JSON implementation of CloudEvents, an open specification of how event data is represented across various platforms.
- Provides a uniform and standard way for publishing and consuming cloud based events, which promotes interoperability across different applications in different environments and platforms.

- Headers for both Event Grid schema and Cloud schema are the same except for the `content-type`
	- For Event Grid, it's `application/json`
	- For Cloud schema, it's `application/cloudevents+json`

## Event delivery durability

- Event Grid will attempt to delivery every event for each appropriate subscriptions immediately.
	- If the subscriber's endpoint did not acknowledge its receipt of the event or if a failure occurred, Event Grid will try to redeliver the event based on a configured retry schedule and retry policy.

- Event Grid has three options in case a failure happens:
	- Retry delivery
	- Place the event in the dead letter queue
		- Dead letter queue is where all failed events or queue messages from being delivered are stored.  ^3af9b7
	- Drop the event based on the error encountered.
- The last two options are more probable when the subscriber's endpoint encountered a configuration error (example: the endpoint was deleted).
	- If the following status codes are encountered, Event Grid will not retry delivering an event: 400, 413, 403
	- If the error returned is not one of the stated status codes above, Event Grid will wait for 30 seconds before it attempts to redeliver the message.
		- Event Grid uses exponential backoff retry policy, where the initial retry will be attempted after 30 seconds, the next retry after 90 seconds, the next after 270 seconds and so on.
	- If the endpoint was able to respond within 3 minutes, Event Grid will try to remove the event from the retry queue and there are **no assurances** that a duplicate will not be created.
- Events are dropped automatically if the previous point has been made AND sending an event to the dead letter queue is not configured for an endpoint.

### Retry Policy

- Uses the following settings for configuring retries.
	- **Maximum number of retry attempts**
		- Default is 30. 
		- Can be any value between 1 and 30
	- **Event TTL**
		- Default is 1440 minutes.
		- Can be any value between 1 and 1440.

### Output batching

- Batch events that are being delivered in order to improve performance.
- This feature is turned off by default
- Has two settings.
	- **Max events per batch**
		- Note that fewer events might be sent if there are no events available at the time the events are published.
		- It will not wait for events to fill up the max amount before it is delivered.
		- Value must be between 1 and 5000.
	- **Preferred batch size in KB**
		- A batch size can only exceed the set size if the size of a *single* event exceeds the max size allowed.

### Delayed delivery

- Event Grid can purposely delay the delivery and retry of events if the endpoint continuously suffers from failures.
- The aim of this feature is to protect the endpoints and Event Grid.
	- Continuously sending out events that will simply go back to the Event Grid because of failure can strain both the Event Grid and your local application.

### Dead letter events

- [[event-grid#^3af9b7|Initial information]]
- Events are place in dead letter once it reaches the maximum number of retries or TTL of the event.
- Turned off by default.
	- To enable it, provide a storage account that will hold the dead letter events.
	- This storage account is where the events will be pulled in case there will be an attempt to retry the event's delivery.
- If the endpoint returned an error status code, Event Grid will immediately schedule an event for dead letter storing.
	- There is a 5 minute delay in this operation.
	- The event will be dropped if the storage account is not available within 4 hours.

## Event access control

- Through RBAC, Event Grid can give different users different levels of accesses to perform different management operations to events such as checking available event subscriptions, creating new subscriptions, and generating keys.

- Roles available:
	- Event Grid Subscription Reader
	- Event Grid Subscription Contributor
	- These two roles are important in establishing event subscriptions as they are needed for sending out events and assigning them to topics.
		- Before creating an event or a topic, a subscription is needed first.

	- Other roles:
		- Event Grid Contributor
		- Event Grid Sender

- Event Grid is not allowing just anyone to send events to a resource.
	- The event source needs the `MicrosoftGrid/EventSubscriptions/Write` permission in order for a resource to have the ability to send events.
		- If writing an event to a *system topic*, permission is applied at the scope of the resource that is publishing the event
		- If writing an event to a *custom topic*, permission is applied at the scope of the event grid topic.

## Receiving events via webhook

- When an event is ready to be sent, Azure Grid will send the request to the provided webhook endpoint with the event in the request body.
- Before Event Grid sends a request to the endpoint, you need to prove that you own the webhook endpoint

- A special process is going to be executed if another endpoint type other than HTTP endpoints (like HTTP trigger based Azure Functions) and is highlighted by these three ways:
	- **Synchronous handshake**
		- When an event subscription is created, Event Grid will send out a subscription validation event to the endpoint.
		- The schema of the event that was sent is similar to the schema that regular event uses.
			- The only difference is the `data` property will contain a `validationCode` property.
		- The client application that implements the webhook should be able to send a response with the send validation code in its body.
	- **Asynchronous handshake**
		- Characterized with the use of a third party service like *Zapier*.
	- **Manual validation handshake**
		- Similar in principle to the synchronous handshake
		- An event will still be sent to the endpoint with an additional property found in the `data` property, the `validationUrl`.
		- Locate the URL and do a GET request either in the terminal or in a web browser.
		- The URL has a validity of only *5 minutes*.
			- The starting status of the subscription while waiting for the validity check on the URL is `AwaitingManualAction`
			- Once the 5 minutes is elapsed, the status will then be `Failed`.
		- Still this requires the webhook endpoint to return a 200 response.
			- Default action is still the automated synchronous handshake.
			- If the webhook endpoint returned a 200 response but has no validation code provided, the process will then be converted to the manual.

## Event filtering

- Options for filtering
	- Event types
	- Subject begins with or ends with
	- Advanced field operators

### Advanced filtering

- This works such that the following values are needed to be provided.
	- `operatorType` - can be a string contains operation or a number is greater than or equal to operation.
	- `key` - The property where the operation will be applied
	- `value` 
		- The value that will be compared to the key with the given operator type.
		- Can be a single value or multiple values stored in an array.