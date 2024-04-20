# Constructors

---

- These are the actions that take place when initializing a new instance.
    - Instance fields are set to 0.
        - Typically done by the runtime.
        - Instance fields are fields that are set to public.
    - Field initializers in the current class run.
        - Field initializers are fields that are set as private.
    - Base type field initializers run.
        - This includes the most base class starting at `System.Object`.
    - Base instance constructors run.
        - This includes the most basic class, similar to the action above this step.
    - Instance constructors of the instantiated class run.
    - [[classes-structs-records#Object Initializer|Object initializer]] run.

- A constructor can be written as this:
    

```jsx
public Class1(int prop1, int prop2)
	: this(prop1 * prop2) {}
```

This means that it will call another constructor in Class1 that can match the parameters set on the `this` call.

### Primary constructors

- Introduced in C# 12
- The parameters defined in the constructor is available to be used by the entire class.
- With this, properties and fields can be defined and initialized inside the constructor.
- Behind the scenes, the compiler generates a private field to hold the value of the parameter in a primary constructor.
    - This is applicable for `class` and `struct` types.
    - For `record` types, the compiler instead creates a public property to hold the value of the primary constructor parameter.

### Static constructors

- Parameterless
- Does not take an access modifier.
- There can only be one static constructor.
- Invoked automatically by CLR.
- One of its primary purpose is to initialize static fields defined in a class.
	- It is a hidden implementation, meaning a class behind the scenes utilizes the static constructor to initialize the static fields defined in said class.

- The following actions are executed when initializing a static class.
    - Static fields are set to 0.
    - Statis field initializers are run.
    - Base type static field initializers run.
    - Base static constructors run.
    - Static constructor runs.
- A branch is available in the `DC.Lab` repository that demonstrates how static constructor is used: `csharp/static-constructor`.

### Private constructors

- You can prevent a class from being instantiated by setting a class constructor as private.
- Generally used for classes that only contains static members.
- Declaring a private constructor prevents automatic generation of a parameterless constructor, meaning the class will not and cannot be instantiated.

### Copy constructor

- Concept of using the type itself to copy the contents of a value of said type.