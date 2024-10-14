# 🐢 .NET CLI
---

- To create a new solution file:

```
dotnet new sln -n <Name of solution> -o <Name of subdirectory>
```

You can omit the -o section to create the solution file on the current directory where this command is executed.

- To create a new C# console application:

```
dotnet new console -lang c# -n <Name of project> -o <Name of subdirectory> -f net8.0
```

-o omission is still possible in this command.

-f section indicates what framework will be using. Use the format string of the framework specified above. It can be net9.0 or net7.0.

- To add a project to a solution

```
dotnet sln <solution file with path> add <project file with path>
```

Add the path if command is ran outside of the directory where the solution and the project files are located.