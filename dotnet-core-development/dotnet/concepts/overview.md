# 🍯 Overview
---

## Introduction

- .NET is a free, open-source developer platform for building many kinds of applications.
    - Developer platform means that every tool or component needed to create the application, .NET has it.
- It can run programs in many different languages, but most are written in C#, which is the most popular option.

### Components

- Runtime - something that executes application code
- Libraries
- Compiler
- SDK and other tools
- Application stacks like [ASP.NET](http://ASP.NET) Core, Windows forms, and WPF.

### Benefits

- Supports multiple languages - C#, Visual Basic, F#
- Shared runtime engine between all .NET languages
- Cross-language inheritance, exception handling and code debugging
- Powerful base library that contains all predefined types
- Simplified deployment model and setup
	- Multiple versions of .NET can co-exist in a single machine.
- Extensive command line support.


---

## Implementations

- Four different implementations:
    - .NET 6 and later versions
    - .NET Framework
    - Mono
    - Universal Windows Platform (UWP)

### .NET

- Previously referred to as .NET Core, is a cross-platform implementation of .NET to handle server and cloud workflows.
    - Can also be used to create desktop apps.
- Implements .NET Standard.

### .NET Framework

- The original implementation of .NET.
- Contains additional Windows-specific APIs useful for Windows Forms and WPF.

### Mono

- Mainly used when a small runtime is required.
- Powers Xamarin applications on mobile OSs and games built using the Unity engine.
- Comes with a JIT compiler, the more often use-case and an AOT (ahead of time) compiler, which is used in platforms as iOS.

### UWP

- Used for building modern, touch-enabled Windows applications and software for IoT.

---

## Class libraries

- Three types of class libraries:
    - Platform-specific
    - Portable
    - .NET Standard
- Mono support all three types of libraries, and is said to be the cross-platform implementation of .NET Framework.
    - .NET Framework can run on Mono runtime without modification or recompilation.

### Platform-specific

- Bound to a single .NET platform (Example: .NET Framework on Windows) which it can take advantage to access platform-specific APIs and dependencies.
- Primary class library for .NET framework.

### Portable class libraries

- Library that is supported by multiple implementations of .NET but has limited capability compared to platform-specific libraries.
- An option is provided to choose which platform is going to be supported.
- “More is less” - The more option for platform support is selected, the less the APIs will be available for use.
    - Assembly needs to find common set of features and APIs that are supported across the platforms selected.

### .NET Standard

- Replacement for both platform-specific and portable libraries.
- They are platform-specific in that they expose underlying capabilities of a specific platform, and they are portable in the sense they work on all supporting platforms.
- .NET Standard exposes a set of library contracts, and each .NET implementation can either support each contract or not at all.
    - This means .NET Standard is supported on platforms that support its contract dependenccies.
- It does not expose the entire functionality of .NET Framework but it has a lot more features that it can expose compared to a portable library.

---

## .NET Standard

- Special set of .NET APIs that are available on multiple .NET implementations.
- If a logic is to be shared between .NET Framework and other .NET implementation, say .NET 5 for example, a .NET Standard library is to be created.
- Has multiple versions, and the higher the version, the larger the number of APIs that are supported and available.
- Higher version of .NET Standard is recommended to be used with the higher versions of other .NET implementations.
- Always target **.NET Standard 2.0+**.

### When to target .NET 8 or .NET Standard

- For reusable app components, use .NET 8.
    - It is best to keep all projects that make up an application have the same versions of .NET.
- For reusable libraries, you can choose to target .NET Standard, but not .NET Standard version 1.0, or the earliest version, as it has low reach.
    - Majority of applications and code nowadays reference .NET Standard 2.0.
    - If .NET Framework is not to be supported use .NET Standard 2.1 or .NET 8.

### Problems

- Slowness in adding new APIs
    - APIs to be added to a version of .NET Standard needs to undergo an extensive period of review.
    - Need to wait a certain amount of time in order for an API to be added to Standard.
    - No more versions are going to be released after .NET Standard 2.1.
- Complex versioning
    - The separation between the API specification and its implementation results in a complex mapping of API specification versions and implementation versions.
- Platform unsupported exceptions at runtime
    - Encountered when a platform does not have an implementation for a specific API implemented in a code that references .NET Standard.

---

## Releases, patches and support

- An application doesn’t automatically roll to a newer version when there’s a new major or minor version of .NET that was introduced.
    - It is highly recommended to test the application first if a new major or minor version is released before using that version to production.
- If a new servicing patch is released, the application automatically uses that servicing patch as it is just a minor change.
    - Automatically using the new service patch version is what you call a runtime roll forward.
- Once a version reaches its End of Support, it will not receive any further updates, one of which are the security patches which is important in ensuring an application’s safety.
    - It is recommended that before a version reaches its End of Support date that the application migrates to a new .NET runtime version.

### Release tracks

- Two support tracks for releases:
    - **Standard Term Support**
        - Supported until 6 months before the next major or minor release ships.
        - .NET 5 is an STS release and was released in November 2020. It was supported for 18 months until May 2022.
        - .NET 7 is an STS release and was released in November 2022. It was supported for 18 months until May 2024.
    - **Long Term Support**
        - Supported for minimum of 3 years.
- Releases alternate between STS and LTS tracks.

### How to choose a release track

- If the application will constantly be updated, an STS release will work.
    - Useful for staying up to date with latest .NET features.
- If the priority is an application’s stability, an LTS release will work.