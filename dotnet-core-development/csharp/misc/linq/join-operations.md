# Join Operations

---

### Overview

- Join is the action of combining data from two different sources based on common values.

- Join is common in queries between data sources that have a common denominator.
    - In object oriented programming for example, this may refer to two models that has a way one relationship.
        - What this means is that the first model may contain information to the second model by having it as a type in one of the properties of the first model, while the second model does not contain any properties linking to the first model.

- LINQ contains join methods `Join()` and `GroupJoin()`
    - Both methods perform “equijoins” or joins that match two data sources based on a common key.
        - In SQL, the key talks to some kind of code or primary key.
    - `Join` implements an inner join
    - `GroupJoin` implements both inner join and left outer join, kinda.
        - It creates a group within a group.
        - Uses the keyword `into` to create this inner group.
        - You need two foreach statements to unwrap an enumerable created with `GroupJoin`

### Perform Inner Joins

[[dotnet-core-development/csharp/misc/linq/inner-joins|Perform inner joins]]

### Perform grouped Joins

- Group join is useful for creating hierarchical data structures.
- An example of this would be having the collections students and courses, and the result of a group join between the two entities is the student and then all the courses registered under that student.
    - This is possible with the student and course entities having IDs that reference each other.
    - A Student may contain a list of course IDs for example.

- A group join is characterized by joining two sequences together and using the into keyword to load all connected values within the two sequences.
    - What this will do is it enables a creation of a new type with a collection property where it can list down all common values between the two given collections based on the provided join condition.
    - This will need two foreach statements to unwrap:
        - One foreach statement to get the immediate property values that may included;
        - Another foreach statement that will cycle through the collection property that was created and populated using the `join...into` statement.

### Perform left outer joins

- Element of the first collection is returned whether or not it has a match with the second collection.
- The method `DefaultIfEmpty` is used here:
    - Its usage involves calling this method from the grouped object that is the result of the `into` operator.
    - What this method does is it provides a default value to the object referenced in the grouped collection.
    - This is helpful for left joins because if the first collection does not have a match in the second collection, the left join operation will still display the first collection without worrying about the matching value.

### Order the results of a join clause

- A sub query is necessary to order a LINQ query after a join.
    - Ordering may not be preserved after declaring a source, then ordering data on that source collection, then joining it with another source collection afterwards.

### Join by using composite keys

- This operation involves comparing two property values instead of the typical one property value comparison for joins.
- When creating composite keys with properties that don’t have similar property names, simply declare a property name on the anonymous object declarations.

### Perform custom join operations

- Most of the join operations are equijoin operations.
    - Equijoin operations is characterized by using equality of one of more property to values to execute the join.

- There are other types of joins that are not equijoin operations:
    - Join is predicated on inequality
    - Introducing a temporary range as the secondary source collection to be used for the join.

- For this types of joins, using multiple from statements are necessary.
- Code samples for this operation can be found on file `CustomJoin.cs` on the `csharp/linq/example-2` branch of the `DC.Lab` project.

**Cross Joins**
- Merging of two collections together with no join criteria defined.
- Be cautious when using this as it can give a large dataset as a result.
- Synonymous to SQL’s full outer join

**Non-equijoin**
- This demonstrates a common operation in SQL where:
    - The first query will get all the IDs of table A and place it in a temporary list variable
    - The second query will get compare the IDs of table B to the derived list of IDs from the first query

- The example shows an equality operation but using a list as a anchor field for comparison