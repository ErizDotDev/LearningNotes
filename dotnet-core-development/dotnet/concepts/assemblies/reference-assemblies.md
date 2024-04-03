# Reference assemblies

---

- Provides metadata to the code that references it. The metadata included are public methods and types.
    - Even though it only lists down public types, objects and methods, it still keeps track of all types and methods, even the private ones.
        - Reference assemblies only care about types or methods that contributes to a code’s public API.

- Reference assemblies don’t contain any implementation code.
    - It acts like an interface or a contract that needs to be implemented.
    - With the current .NET development workflows, an assembly can both be a reference assembly and an implementation assembly.

    - To implement a separate reference assembly and implementation assembly, separate projects will need to be created for both.
        - In the official dotnet Github repository, they implement a reference assembly by:
            - Having a separate folder containing only the metadata that is needed for the code’s public API.
            - Using partial classes to separate the declaration of the methods and types and its implementation albeit in different Visual Studio projects.

- Reference assemblies help with Visual Studio’s Intellisense as it gives users information about a particular type, or object’s properties and how a method works with information about its signature being accessible to the users.

- Reference assemblies are usually the code that we see when we hit the main dotnet core libraries such as `System.IO`, `System.Text` etc.
    - Technically the code that we see when we navigate these methods are the decompiled code from the assembly itself. The assembly is a compiled IL code.

- When a reference is being added to a project via the Add Reference option, Visual Studio automatically finds the corresponding reference assembly for that assembly and for that assembly’s specific version.

- Reference assembly only changes when its public API is affected since it does not care about implementation.
    - Using reference assembly as an input instead of the implementation assembly saves time in build as it has a smaller size compared to an implementation assembly.

- Reference assembly contains method members that may have a `throw null` body.
    - These methods need to have a body (and providing the simplest implementation possible, thus the `throw null` body) so that it can pass the validation of a metadata’s completeness.