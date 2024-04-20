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
		- Default TTL is 7 days.
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