# Perform inner joins
---

- There are four variations:
    - Simple inner join     
        - Reminiscent of the inner join in SQL.
        
        ```csharp
        var query =
        	from person in people
        	join pet in pets on person.Id equals pet.Owner.Id
        	select new
        	{
        		OwnerName = person.FirstName,
        		PetName = pet.Name
        	};
        ```
        
    - Composite key join
        - Using multiple properties to derive on a result.
        
        ```csharp
        var query = 
        	from employee in employees
        	join student in students on new
        	{
        		employee.FirstName,
        		employee.LastName
        	}
        	equals new
        	{
        		student.FirstName,
        		student.LastName
        	}
        	select employee.FirstName + " " + employee.LastName;
        ```
        
    - Multiple joins
        - Given three collections, A, B, and C and the code below, multiple joins in LINQ works such that
        
        ```csharp
        var query = 
        	from aItem in A
        	join bItem in B on aItem.Id equals bItem.Id
        	join cItem in C on new
        	{
        		Prop1 = aItem.PropertyA,
        		Prop2 = bItem.Name.Substring(0, 1)
        	} equals new
        	{
        		Prop1 = cItem.UnknownProperty,
        		Prop2 = cItem.Name.Substring(0, 1)
        	}
        	select new
        	{
        		BName = bItem.Name,
        		CName = cItem.Name
        	};
        ```
        
        - LINQ will evaluate the first join between collections A and B and the condition provided.
        - Then, it will evaluate the join with C with the other data participant being based on the result of the first join between A and B.

    - Inner join by using grouped join        
        - Demo code is in the `linq-2` branch under the `InnerJoinByGroupedJoin` branch.
        - On this example, this code snippet below demonstrates that the join will create a new collection combining the two objects being joined together and thus can be used into another query as another data source, evidenced by the second from:
        
        ```csharp
        var query1 =
                    from person in people
                    join pet in pets on person equals pet.Owner into gj
                    from subpet in gj
                    select new
                    {
                        OwnerName = person.FirstName,
                        PetName = subpet.Name
                    };
        ```
        
        - The result of the into `into` operation, is that the first collection will have another property containing all the matches found in the second collection.
            - If no matches were found, the collection property added to the first object collection will be empty.