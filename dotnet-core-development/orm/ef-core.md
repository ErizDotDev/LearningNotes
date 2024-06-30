# Entity Framework Core
---

### How does EF Core handle concurrency conflicts?

- Concurrency conflicts happens when two different processes attempt to update the same row of data in the database.
- EF Core uses *optimistic concurrency* - meaning it assumes that conflicts will be rare.
	- Optimistic concurrency does not use locks. 
	- Instead it uses <u>concurrency tokens</u>, which is normally saved in the database as the Version field.
		- Concurrency token will act just like a normal property or field in the class.
	- Saving a record will fail it the token retrieved from the database from a query is different from the one used on the update or delete operation.
		- When generating a SQL query for this, the UPDATE or DELETE statement will have a where clause that uses the Version field or the concurrency token.
	- In contrast, *pessimistic concurrency* mainly uses locks.
		- Only the process that triggered the locking can modify the data.