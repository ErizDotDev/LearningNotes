# ⭐ Assemblies
---

- Essentially, they are files that are generated when a C# project is compiled, and these files are distributed and deployed to run C# code.

- Can be a dynamic (dynamic assembly) link library (DLL) or an executable file (static assembly).
    - Dynamic assemblies are run directly from memory and are not saved in the disk before execution.

- Assemblies can contain one or more modules.
    - Modules are typically set of source code files compiled together.
    - An example of this relationship would be the System.IO.dll file and the classes and types contained within this assembly
        - In this case `System.IO` is the assembly.
            - Inside `System.IO` are various classes and types organized under the `System.IO` Visual Studio project.

- Provides [[dotnet-core-development/csharp/getting-started/introduction#^1826a9|CLR]] with information it needs to know type implementations.
    - Types only exist within an assembly.

### Properties

- For libraries that target .NET framework, sharing assemblies between applications is typically by placing the assemblies in the Global Assembly Cache (GAC)
    - Assemblies must be strong-named first before being included in the GAC.
- Assemblies are only loaded to memory when required.
    - Contributes to efficiency when managing resources in large projects.
- Programmatically obtain information about an assembly using reflection.

### Assembly contents

- Standard static assembly contains the following:
    - [[assemblies#Assembly manifest|Assembly manifest]] - required
    - Type metadata
    - Compiler generated MSIL code that implements the type
    - Resources

### Assembly manifest

- Contains the assembly’s metadata. (security, version, scope, assembly references, list of files)
- An assembly file can contain multiple files in it, known as a multi-file assembly.

### Versioning

- Follows this format: _`major.minor[.build[.revision]]`_
    - The square brackets indicate the optionality of the version component, which means:
        - Build is optional and
        - Revision is optional but build must exist in order to have a revision.
    - Major - Reflects major changes; Backwards compatibility is not assured;
    - Minor - Can be backwards compatible
    - Build - How many times a source code is built
    - Revision
- For versions to be equal, the `Version` objects should have identical components across the board.
- If the version object is undefined, that `Version` object is considered to be earlier than a `Version` object that has a build or revision number equal to 0.

### Side-by-side execution

- Assemblies enable side-by-side execution - or the ability to run multiple versions of runtime and multiple versions of an application or a component of an application simultaneously in one machine.
- This ties together with strong naming.
    - Strong naming allows the Global Assembly Cache to store an assembly with multiple different versions.
- Needs some setup in order to create an application that does side-by-side execution.

### Unloading assemblies

- .NET Framework uses custom app domains to unload assemblies. .NET Core only uses a single default app domain.
- Unloadability can be done using the `AssemblyLoadContext` class.
- Difference of unloading assemblies in .NET Framework and the latest .NET:
    - .NET Framework deals with forced unloading meaning all threads running in the target app domain is closed, managed COM objects are destroyed, and so on.
    - .NET unloads assemblies in a cooperative fashion. It waits essentially until no resources are being used and all objects utilized are garbage collected.
- **Practical use cases**
    - Plugin systems
    - Dynamic code generation
    - Hot reload
    - Applications that need dynamic extensibility

### Reference assemblies

[[dotnet-core-development/dotnet/concepts/assemblies/reference-assemblies|Reference assemblies]]

### Resolve assembly loads

- Uses `AssemblyResolve` event to provide users more control over their assembly loading.
- `AssemblyResolve` event is raised practically when the assembly is being loaded through methods such as `AppDomain.Load`, `Assembly.Load`, `Assembly.LoadWithPartialName` etc.
- Event handler will return null if it did not find the assembly name provided.
- `AssemblyResolve` event handler can return a different version of the assembly than the version that was requested.

### Assembly names

- A strong assembly name contains the assembly’s name, culture, version, and public key.
    - This is regarded as an assembly’s fully qualified name.
    - Optionally, it can also include the processor architecture.
- When attempting to load an assembly, specifically .NET Framework assemblies, it is important to always use the fully qualified name.
    - This is called qualified binding.
- Partial binding, not using the fully qualified name of an assembly, is not allowed, particularly when loading .NET Framework assemblies.
- If you have assemblies of the name, for instance an executable named TestAssembly.exe that references TestAssembly.dll, it is important to:
    - Place these assemblies in different directories; or
    - Assign different names for each assembly.
    - The runtime does not consider the file name when determining an assembly’s identity.
        - It will not bind the executable correctly to its assembly because the assembly shares the same name if the executable is executed via code. (Specifically via `AppDomain.ExecuteAssembly`.
- Different ways to find an assembly’s fully qualified name:
    - For assemblies that are hosted in the GAC, use gacutil or the GAC utility tool.
    - For assemblies that are not hosted in the GAC:
        - Use IL diassembler (ILdasm.exe) tool
        - Using `Assembly`'s `FullName` property or `AssemblyName.GetAssemblyName` method.

### Assembly locations

- Assembly should be located only on the following locations:
    - Inside an application’s sub-directory
    - GAC
    - On an HTTP server

### Assembly attributes

- Assign attributes either in code or in the project file.

### Strong named assemblies

[[dotnet-core-development/dotnet/concepts/assemblies/strong-named-assemblies|Strong named assemblies]]

### Delay sign assemblies

- Used in scenarios where the private key is made unavailable to a certain few individuals, while maintaining public access to the public key.
    - Delay sign makes it possible to only use the public key during the development, and then sign, or provide the actual strong name signature, when the assembly is to be shipped.
- Behind the scenes, the compiler will only insert the public key into the assembly manifest and leave space for the signature in the PE file.
- To achieve delay sign, an attribute, `AssemblyDelaySignAttribute` needs to be provided.

### View assembly contents

- Use the IL Disassembler (ILdasm.exe) tool.
- Using `MetadataLoadContext`
    - For reference assemblies and assemblies compiled for another platform or process architecture.
    - Assemblies opened through this route can only inspect contents, not execute any code in it.

### Type forwarding

- Moving a type to another assembly without recompiling applications that use the original assembly.
- Example scenario: You have an existing assembly named _Utility.dll_. Inside this assembly is a type named _Example_. Now the developers decide to move _Example_ into another assembly.
    - After compiling this change, the application that uses _Utility.dll_ will fail because it is expecting the _Example_ type which does not exist anymore in this new version of the assembly.
- This problem can be avoid by forwarding requests for class _Example_ to the new containing assembly of the type using the `TypeForwardedToAttribute` which is placed in the original assembly that initially contained the transferred class/type.

### Friend assemblies

- Assembly that can access another assembly’s internal types and members.
- Use `InternalsVisibleToAttribute` to define one or more friend assemblies.
- Useful in unit tests when an internal method needs to be in scope of testing.
- Mathematical transitive property does not apply with friend assemblies
    - Assembly A is a friend of Assembly B and Assembly B is a friend of Assembly C. Assembly C does not have access to internal types of Assembly A.
- Validations are still in place to check the friend assembly name passed to the `InternalsVisibleToAttribute`
- You can also use `StrongNameIdentityPermission` to share types
    - It shares some similarities with `InternalsVisibleToAttribute`
    - Its main difference lies with the scope of its effect.
        - `InternalsVisibleToAttribute` controls visibility of internal methods and types while
        - `StrongNameIdentityPermission` controls security permissions on specific sections of code.
- **Unsigned friend assembly -** One of the friend assemblies is unsigned.
- **Signed friend assembly** - Both of the friend assemblies are signed.

### Checking if file is an assembly

- Through ILdasm tool
- Through code:
    - `AssemblyName.GetAssemblyName` method.
        - If `BadImageFormatException` is returned, file is not an assembly.
    - `PortableExecutable.PEReader` from `System.Reflection.Metadata` package
        - `HasMetadata` property - true means file is an assembly
        - Expects a `FileStream` input.

### Embed types from managed assemblies

- This allows a program to use types from any version of a managed assembly without having to recompile that program for each new version.
- Often seen with using COM interops such as applications that uses objects from MS Word.
    - This allows the application to work with different versions of MS Word on different computers.
- A code sample demonstrating how this is achieved is available in the `DC.Lab` solution under branch `dotnet/assembly/embed-type-info`

### Notes

- The `sn`, `al`, and ILdasm tools can only be accessed using the Visual Studio command line.

### Sample Code

- `dotnet/assembly/embed-type-info`
- `dotnet/assembly/generic-types`