# 🚪 C# 

## Classes

## Access Modifiers

[[program-building-blocks#Accessibility|✏️ Description]]
[[members#Access Modifiers|✏️ Additional Notes]]

## `virtual` keyword

- If a property or a method is applied with this keyword, it means that a sub-class can override the implementation of said property or method.
- You cannot apply this keyword to a partial method. ([[classes-structs-records#^4b0e49|Info]])
- Some rules to follow when updating virtual methods or properties in a versioned application or library ([[csharp-concepts#**Versioning**|Info]])
- Events cannot be declared as virtual. ([[dotnet-core-development/csharp/concepts/events#^f3721d|Info]])
- A derived class can use `sealed` keyword to disregard a virtual property or method. ([[members#Abstract and sealed classes and class members|More info]])
- A cool trick using virtual methods ([[members#^4f17bc|Info]])
- Here are some ways on how virtual methods or properties can be utilized in polymorphism. ([[polymorphism|Info]])


This section highlights how the old notes are compiled. These notes are compiled based on the order that they were studied. Author has read the Microsoft official documentation for C# in below order.

## Getting Started
---

[[dotnet-core-development/csharp/getting-started/introduction|⭐ Introduction]]
[[dotnet-core-development/csharp/getting-started/types-members|⭐ Types and members]]
[[dotnet-core-development/csharp/getting-started/program-building-blocks|⭐ Program building blocks]]
[[dotnet-core-development/csharp/getting-started/major-language-areas|🏓 Major language areas]]

## Fundamentals
---

[[dotnet-core-development/csharp/fundamentals/fundamentals|🏓 Fundamentals]]
[[dotnet-core-development/csharp/fundamentals/records|🏓 Records]]
[[dotnet-core-development/csharp/fundamentals/interfaces|⭐ Interfaces]]
[[dotnet-core-development/csharp/fundamentals/anonymous-types|🏓 Anonymous Types]]
[[dotnet-core-development/csharp/fundamentals/functional-techniques|🏓 Functional techniques]]
[[dotnet-core-development/csharp/fundamentals/exceptions|⭐ Exceptions and errors]]
[[dotnet-core-development/csharp/fundamentals/coding-style|🏓 Coding Style]]

## C# concepts
---

[[dotnet-core-development/csharp/concepts/csharp-concepts|🏓 C# Concepts]]
[[dotnet-core-development/csharp/concepts/nullable-types|🏓 Nullable reference types]]
[[dotnet-core-development/csharp/concepts/nullable-migrations|🏓 Nullable reference migrations]]
[[dotnet-core-development/csharp/concepts/methods|🏓 Methods]]
[[dotnet-core-development/csharp/concepts/properties|🏓 Properties]]
[[dotnet-core-development/csharp/concepts/indexer|🏓 Indexer]]
[[dotnet-core-development/csharp/concepts/iterators|🏓 Iterators]]
[[dotnet-core-development/csharp/concepts/generics|⭐ Generics]]
[[dotnet-core-development/csharp/concepts/delegates|⭐ Delegates]]
[[dotnet-core-development/csharp/concepts/events|⭐ Events]]
[[dotnet-core-development/csharp/concepts/distinguish-events-delegates|⭐ Distinguishing delegates and events]]

## Programming concepts
---

[[dotnet-core-development/csharp/programming-concepts/contravariance-covariance|🏓 Covariance and contravariance]]
[[dotnet-core-development/csharp/programming-concepts/statements-expressions-equality|⭐ Statements, expressions, and equality]]
[[dotnet-core-development/csharp/programming-concepts/types|🏓 Types]]
[[dotnet-core-development/csharp/programming-concepts/classes-structs-records|⭐ Classes, structs and records]]
[[dotnet-core-development/csharp/programming-concepts/strings|⭐ Strings and string literals]]

## Advanced topics
---

[[dotnet-core-development/csharp/advanced/reflection-attributes|🏓 Reflection and attributes]]
[[dotnet-core-development/csharp/advanced/expression-trees|🏓 Expression trees]]

## Miscellaneous
---

[[dotnet-core-development/csharp/misc/indices-ranges|🏓 Indices and ranges]]
[[dotnet-core-development/csharp/misc/linq|⭐ Language-Integrated Query (LINQ)]]
[[dotnet-core-development/csharp/misc/async-programming|⭐ Asynchronous programming]]
[[dotnet-core-development/csharp/misc/miscellaneous|🏓 Miscellaneous]]


### To-Do

- [ ] Re-read and re-analyze these notes and the article once you have a good grasp on the stack and heap concept.