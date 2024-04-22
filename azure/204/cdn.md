# ☮️ Azure CDN
---

- A **Content Delivery Network (CDN)** is a network of servers that provides fast and efficient web content delivery to users.
	- What makes it fast and efficient is that they store *cached* content on edge servers in locations that are close to end users, minimizing latency.
	- They can also accelerate distributing content *without caching* by using network optimizations utilizing CDN **Point of Presence (POP)** locations such as:
		- Using route optimization to bypass Border Gateway Protocol.

- **Features:**
	- Dynamic site acceleration
	- CDN caching rules
	- HTTPS custom domain support
	- Azure diagnostics logs
	- File compression
	- Geo-filtering
		- Allow or block content from any country(ies) or region(s)
		- For Standard tier, you can only allow or block an entire site.

- **Benefits:**
	- Better performance and experience to end-users
	- Scaling to handle instantaneous high loads
	- Distributes user requests and serving content directly from edge servers to lessen requests being sent to the original server.

### How it works?

1. User makes a file request by accessing a specific URL.
	- The DNS will route the request to the POP location closest to the user.
		- Usually the closes POP location to the user is also the best performing POP location.
2. If no edge servers in the POP location have the file in their cache, they will send a request to retrieve the file from the origin server.
3. The origin server will return the file to the edge server.
4. Edge server will cache the file and returns the file to the user.
	- The file will remain in cache until the TTL specified by the HTTP header expires.
		- Default TTL is 7 days for general web content delivery optimizations.
			- Other default TTL values:
				- Large file optimizations: 1 day
				- Media streaming optimization: 1 year
		- TTL duration can be verified on the `Cache-Control` of the HTTP response from the origin server.
5. If the file's TTL did not expire, the edge server will return the file directly from the cache.

### Requirements for use

- To use Azure CDN, a CDN profile must be created.
	- A CDN profile is a collection of CDN endpoints which represent their own content delivery behavior and access.
- CDN endpoints can be organized in different ways using multiple profiles.
	- **NOTE:** Azure CDN pricing is applied at the profile level.
- **Important:** Take note of the following CDN resource limits:
	- Only a limited amount of CDN profiles can be created.
	- Only a limited amount of endpoints can be created in every CDN profile. 
	- Limited amount of custom domains can be mapped to a CDN endpoint.

### Controlling cache behavior

- A cache resource can be out of date or stale when its age is less than the age or period set in the cache settings.
	- In addition, cache does not compare the resource in its storage to the object found in the origin server to save time and bandwidth. (also for performance reasons)
- That is why it is important to control when the cache content is refreshed.
- Cache will always assume that the current object in its storage is the latest version.
- When it validates the object and its age and if it fails this validation, then that's the only time the cache will get the latest copy of the resource from the server.

- There are two mechanisms provided by Azure for caching files:
	- For other tiers other than Azure CDN Standard:
		- Caching rules can be standard or custom.
			- Custom rules apply to specific paths and file extensions.
		- **Query string caching**
			- Defines how Azure CDN will respond to query strings
			- Has no effect on files that can't be cached.

	- For Azure CDN Standard:
		- **Ignore query strings**
			- Default option.
			- CDN will immediately send request along with all query strings to the origin server on the first request and caches the response.
			- Subsequent requests will ignore query string until the asset expires.
		- **Bypass caching for query strings**. Query request will be sent to the server and the response will not be cached.
		- **Cache every unique URL**
			- Every time a client generates a unique request URL, the URL is passed to the origin server and its response will be cached.
			- This is inefficient because since a unique request URL can mean the response will also be different for each URL requested.
				- This means the number of times a cache can serve a specific request given its URL can be low.

### Content updating

- To ensure that the client will always have the latest version of an asset, consider adding a version string in the asset URL.
	- This will prompt the CDN to retrieve a new asset immediately.

- Another approach for updating content is to purge the cached content from the edge nodes.
	- Do this if the application is on a new version or when replacing out of date assets.
	- How?
		- Purge cache on endpoint to endpoint basis, or all endpoints at once.
		- In Azure Portal, select the file by including the path to the file or all assets on a selected endpoint and clicking the **Purge All** option.
		- Using wild cards (`*`) or the root(`\`).

- Available CLI commands for purging cache from an endpoint.

```bash
az cdn endpoint purge \
	--content-paths '/css/*' '/js/app.js' \ #Example content paths
	--name <endpoint_name> \
	--profile-name <profile_name> \
	--resource-group <resource_group_name>
```
^3c9a7e

- CLI command that will pre-load assets to an endpoint.
	- Useful for applications that create large number of assets and needs to be pre-loaded before any actual requests were received:

```bash
az cdn endpoint load \
	--content-paths '/img/*' '/js/module.js' \ #Example content paths
	--name <endpoint_name> \
	--profile-name <profile_name> \
	--resource-group <resource_group_name>
```
^44b11d

### Interacting with CDN using .NET

- Install this package `Microsoft.Azure.Management.Cdn`
- Instantiate first a `CdnManagementClient` object.
	- Through this client object, the `Profiles` and the `Endpoints` property can be accessed.
		- Use the above properties to create or purge an endpoint.
			- A profile should be created first before an endpoint can be created.

- To create a profile, use the `ProfileCreateParameters` object that will be passed to the `Profiles.Create` method.
- To create an endpoint, use the `EndpointCreateParameters` object that will be passed to the `Endpoints.Create` method.
- To purge an endpoint, use the `Endpoints.PurgeContent` method.