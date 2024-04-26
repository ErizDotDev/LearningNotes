# ☮️ Azure Message Queues
---

- Supports two types of queues.
	- **Service Bus queues**
		- Part of Azure messaging infrastructure that enables integration of different application or application components by supporting queueing, pub/sub and more advanced integration patterns.
	- **Storage queues**
		- Part of Azure's storage infrastructure that allows the storage of a large number of messages that can be accessed anywhere in the world using HTTP or HTTPS.

- *Queues* are associated with the collection of different messages that are lined up for processing in an asynchronous fashion. ^154a16
- *Messages* is a data container and can be one of the following structured data formats: JSON, XML, Plain Text, etc.

### When to use Service Bus queues?

- Application needs to receive messages without polling the queue.
	- This is achieved as Service Bus uses long polling receive operation using TCP based protocols.
- Application requires the queue to deliver messages in first in first out (FIFO) basis.
- Application needs to support automatic duplicate detection.
- The application should process messages as parallel long-running streams.
	- Parallel long running streams rely on the application's nodes to compete for streams and not messages.
	- When a node consumes a stream, the node can check the state of the application stream using transactions.
- The application requires that messages in a queue or the queue itself are processed in a way that either all messages in the queue are processed correctly and successfully or none of the messages will be processed.
	- This behavior is known as transaction behavior and atomicity.
	- This will promote consistency and reliability in your applications.
- The application can process messages with size of 64KB but not exceeding 256KB.

### When to use Storage queues?

- Application must store over 80GB of messages in a queue.
- Application needs to track the progress of the processing of message in a queue.
	- Useful for scenarios when a particular worker or process thread failed processing, and another thread can inherit the work.
		- The next thread or worker can get the information from the previous run and work their way through the problem.
- Logs are required for every transaction executed against your queue.

## Service Bus

### Tiers

- Offers a Standard and a Premium tier.
- Premium tier is recommended for production scenarios because of its performance and flexible scalability options.
- ==Standard== has a variable ==pay as you go pricing== while the ==Premium tier== has ==fixed pricing==.

### Advanced features

- **Managed sessions**
	- Guarantees FIFO delivery of messages.
	- Sessions enable the exclusive handling of a continuous stream of related messages without a predefined limit, thus promoting message sorting reliability to implement FIFO and scalability.

- **Autoforwarding**
	- A queue is chained to another queue as part of a workflow or a process as defined in an application architecture.
	- Given an ordering system and having several different queues representing the different stages an order goes through: `OrderQueue`, `VerificationQueue`, `FulfillmentQueue` and `TrackingQueue`
		- Autoforwarding can be configured so that messages in the `OrderQueue` can be forwarded to the `VerificationQueue` for processing, and so on...

- **Dead letter queue**
	- Storage that holds the letters that failed delivery.
	- Users can go through the DLQ to inspect the failed messages.

- **Message deferral**
	- An application can request for a message to be deferred, where Service Bus can keep the message in an inactive set until the application requests for the same message again.
	- Useful for scenarios where a form cannot be processed because it lacks information or data that a user needs to provide.
		- Application will send the incomplete form to the queue and defer for it later.
		- When the user has provided all the necessary information, it will send out a request or a message with the updated information, find the ticket in the queue's inactive set and finally forward the message to the queue.

- **Transactions** 
	- Enables the processing of messages to be performed by a grouped set of operations operating under the scope of a single transaction.
		- This means that in a collection of messages, if one of the messages fail, the other gets failed as well.

- **Autodelete on idle**
	- An idle time is defined for messages before they get deleted in the queue.
	- Minimum duration is 5 minutes.

- **Filtering and actions**. Subscribers can define specific messages they wanted to receive from a topic by defining filters through subscription rules. ^1bc7d6
- **Scheduled delivery**. Submit messages on a specific date and time that allows an application to execute delayed processing for such messages.
- **Batching**. Collects messages until a certain batch size was reached or a configured wait time has elapsed before sending the collected messages to the Service Bus which can cause some delay.
- **Duplicate detection**. Azure can detect and discard duplicate copies of a message giving the client confidence to continue to send messages.
- **Security protocols**. Supports Shared Access Signature (SAS), [[identity-access-security#Role-based access control|RBAC]] and Managed Identities for Azure resources.
- **Geo-disaster recovery**. This feature will kick in so that the queue can be used in a different data center or region in case Azure experiences downtime in the region where the queue is registered.
- **Security**. Supports HTTP/REST protocols and AMQP 1.0, a message protocol that follows the ISO standard that allows integration with ActiveMQ or RabbitMQ.

### Concepts

**Queues**
- [[message-queues#^154a16|Initial information]]
- Delivered in a FIFO fashion.
- Messages are persisted safely which allows producers and consumers to operate independently from one another, promoting decoupling both as application components and communication mediums.
	- Producers can send as much messages as they want without any consequence, and consumers can process these messages as they come.
		- This process is known as *load leveling*, producers and consumers send/receive messages respectively at different rates.

**Receive modes**
- Azure Service Bus offers two modes:
	- ***Receive and delete***
		- Simplest mode to work with.
		- When the Service Bus receives a request from the client, it marks the message as consumed and returns it back to the consumer.
		- Works well for scenarios where it is fine for an application to miss the processing of a message if a failure occurs.
	- ***Peek and lock***
		- Not recommended for applications that can't tolerate missing any messages.
		- Receive operation becomes two stage:
			- Finds the next message to be consumed and locks it so that no other consumer can receive it, and then returns the message back to the client app.
			- After it receives the response back from the client app, will it mark the message as consumed.
		- If the application fails to process the message, Service Bus will unlock the message so that it can be re-consumed by the same application or consumed by another application.
		- A timeout is also associated with the lock, which means if the application message fails to process the message within the timeout period, Service Bus will then unlock the message and allows it to be consumed once again.

**Topics and subscriptions**
- Topics are like queues but a much more scaled version capable of sending out messages to a ton of virtual queues (or subscriptions) which enables them to communicate with a lot of application components.
- A message will become available to all the subscriptions attached to a topic.
	- Publishers will send a message and the message will be available to one or more subscribers registered to the topic.
		- Publishers still send message the same way like they do to a queue.
		- Consumers however don't receive the message directly from the topic but from the topic subscription and they receive it like how they normally receive from queue.

**Rules and actions**
- Refer to this section about [[message-queues#^1bc7d6|filters and actions]] which essentially tells the same thing that filtering can be done as applications tend to process specific set messages in different ways.