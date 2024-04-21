# 🍯 Miscellaneous
---

## .NET Remoting

- A framework that provides an approach to communication between a server client by utilizing **creation of remote objects** that each can access to gain methods or properties to be used by either the server or the client.
    
- Sort of like REST API as they are used with distributed systems for communication.
    - REST APIs uses text based protocols like JSON for communication while .NET Remoting uses binary communication protocol which are **more efficient** in terms of serialization and deserialization.
    - REST API is used more because it is simple, it is adopted by tons of applications and compatibility with various platforms.

- Much like C# class libraries they allow sharing of code between different system components.
        - .NET remoting specializes in cross application communication.
        - Imagine having two applications, a payroll system and an HRIS system, in different codebases, but both use the .NET ecosystem. One application may be written in C#, one application may be written in F#.
        - .NET remoting can be used to share code between the two applications even though they are in different codebases.
    - Class libraries are used in more simple setups where the processes are still in scope of a single application.
        - Using the example above, the payroll system will be the entire focus and it can share code between components inside the system.
            - Example: Salary calculation module can be used to compute employee of type Officer and employee of type Regular.

### `MarshalByRef`

- A C# base class that is used to create remote objects for .NET remoting.

### Reading List

- [https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-netod/bfd49902-36d7-4479-bf75-a2431bd99039](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-netod/bfd49902-36d7-4479-bf75-a2431bd99039)

---

## `ContextBoundObject`

- C# base class used to create objects within a specific context - meaning additional data can be attached to this object during its lifetime.
- Can be used in scenarios involving transaction management, object-level security and custom context-related features.

---

