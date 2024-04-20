# ☮️ Azure Cache for Redis
---

- Caching involves copying frequently accessed data to a location that is close to the application.
	- This process improves the performance, meaning response times, and efficiency of your application.

- Utilized Redis for their in-memory caching mechanism, with options to use either the open source version or the enterprise version from Redis Labs.
	- Both versions are fully managed and hosted by MS and Azure.
	- Can be used by any applications within or outside Azure.

- Useful in the following scenarios:
	- Since databases are too large to load into a cache, applications load data into the cache only when needed.
		- When changes are made into the data, the system can also update the data in the cache and other clients that access the same cache will have updated data as well.
	- Provides storage to static webpage templates that websites often use so that they can be quickly accessed.
	- Reducing the load on cookies that are commonly used in shopping carts or user history data.
		- Instead data will be stored in the cache and the cookie will act as the key to obtain corresponding data and values within the cache.
	- Job and message queueing for long running tasks and operations.
	- Supports executing a batch of commands stored in a cache in a single transaction, with the ability to rollback to the initial state when a command encounters an issue.

### Service Tiers

- **Basic**
	- Redis is running in a single VM.
	- No SLA
	- Ideal for development, test and non-critical workloads.

- **Standard** 
	- Running in two VMs in a replicated configuration.
	- Start here if cache will be used for production workloads.

- **Premium**
	- Offers Redis cache with better performance (high throughput, low latency, and more features)
	- Deployed in more powerful VMs compared to the first two tiers.

- **Enterprise**
	- Cache is powered by the variant from Redis Labs.
	- Supports Redis modules like RediSearch, RedisBloom, etc.
	- Offers higher availability than Premium.

- **Enterprise Flash**
	- Offers cost-effective large cache capacities.
		- Extends data storage to nonvolatile memory, which is cheaper than DRAM on a VM.
		- This reduces the per-GB memory cost.

## Configuration

- The following parameters should be configured when setting up Azure Cache for Redis.
	- **Name**
		- Like Azure Storage, the Redis name should be a globally unique name within Azure as a public facing URL will be generated and used to connect and communicate with this service.

	- **Location**
		- Application and its cache should be in the same location.
			- Setting up the application and cache to be in different location will increase latency and becomes less reliable.

	- **Cache Type** - The service tier.

	- **Clustering support**
		- Allows automatic dataset splitting across different nodes.
		- To implement, specify the maximum number of shards that will be provisioned (max of 10).
		- Cost calculation is the cost of the original node times the number of shards generated.
		- Only available for Premium and above tiers.

### Accessing the Redis instance in the command line

- Redis has a command line tool available on all platforms.

- Common commands
	- `ping`
		- Pings the server.
		- Responds with "PONG".
	- `set [key] [value]`
	- `get [key]` - Get a value associated with a key.
	- `exists [key]` - Returns `1` if the key exists, or `0` if not.
	- `type [key]` - Returns type associated to a value for the given key.
	- `del [key]` - Deletes a value associated with the key.
	- `flushdb` - Clears out the entire cache contents.

### Add expiration time to values

- A time to leave (TTL) is applied to a key to ensure that a key and its value will expire once it becomes stale.
	- This is to make the cache lean and maintain its performance.
	- TTL can be set in seconds or millisecond precision
	- Resolving an expire time takes 1 millisecond.
	- Redis saves the date when a key expires.

### Accessing the cache from the client.

- Client will need to have the host name, port and access key to access the cache.
	- Access Key can be obtained from the Access Keys page.
	- Host name is the URL generated when a name is provided to your cache instance.
	- Similar to [[api-management#^881414|API management]], access keys are generated with a primary and a secondary key.

## Interact with Redis in .NET

- Through Redis library in .NET: `StackExchange.Redis`.
- A connection string can be used in Azure to connect to a specific Redis instance that contains the following required parameters (along with other optional parameters):
	- The URL generated from the host name.
	- `ssl` which accepts a boolean value to make the communication encrypted.
	- `abortConnection` which allows a connection to be created even if the server is unavailable at that moment.

### `ConnectionMultiplexer`

- The main connection object for `StackExchange.Redis` is the `ConnectionMultiplexer`.
	- This object is created by calling the `ConnectionMultiplexer.Connect` or `ConnectionMultiplexer.ConnectAsync` methods passing in the connection string or a `ConfigurationOptions` object.

- Once `ConnectionMultiplexer` instance is created, you can now do the following:
	- Access the Redis database.
		- The Redis database is represented by the `IDatabase` interface.
		- Can be retrieved using the `GetDatabase` method.
	- Utilize the pub/sub features of Redis.
	- Access a server for maintenance/monitoring.

### Common commands

- `set [key] [value]` - `StringSet(key, value);`
- `get [key]` - `StringGet(key);`
- `exists [key]` - `KeyExists(key)`
- `type [key]` - `KeyType(key)`
- `del [key]` - `KeyDelete(key)`
- `CreateTransaction`
- `KeyRename`
- `KeyExpire`
- `KeyTimeToLive`
- `Dispose` - Closes all connections and shuts down the communication to the server.
- `Execute` or `ExecuteAsync` 
	- Run CLI commands that have no available library method like `ping`.

- All commands are called from the `IDatabase` interface.

- The get and set commands can store binary data in both the key and value.
	- Redis keys are of type `RedisKey` which can be implicitly converted to `string` and `byte`.
	- Redis values are of type `RedisValue` which has the same conversion characteristics as the key.

- Aside from binary data (binary-safe strings), Redis can store complex structures like data graphs from JSON or XML as values.
	- The structure should be deserialized first into a valid string.
