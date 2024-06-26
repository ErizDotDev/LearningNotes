# 🏓 Nullable Reference Migrations

---

- Applicable for large projects
- For smaller projects, enabling the feature in the `.csproj` file and immediately addressing the warnings is the way to go.

### Setting the default for the project

- Here are the following default settings that you can apply:
    - _Disabling nullable as default_ - Default setting if the Nullable property is not found in the project file.
        - The strategy here is using nullable directives on each file to properly update the code to use the nullable features.
        - Works for stable library and projects, and should be applied for development efforts focusing on refactoring the codebase to recognize nullable and non-nullable values.

    - _Enabling nullable as default_ - Set this as default when actively working on new features in the codebase so they can benefit on the new nullable features.
        - To start, each code file shall have a directive disabling the nullable features.
        - Slowly work towards each file, removing the directive and addressing the warnings that will be generated by the compiler.
        - The main disadvantage is the additional work of adding disabling nullable directives on every file.
        - The advantage of this approach is that new code that is being added is now nullable aware
            - meaning the nullable handling approach can be applied from the get-go.

    - _Nullable warnings as default_ - Use this for a two-phase migration approach.
        - The first phase is removing the compiler generated warnings.
        - The second phase is adding the appropriate annotations on variables to state their expected null-state.

    - _Nullable annotations as default -_ Annotate code first before warning.

### Understanding contexts and warnings

- _oblivious_
    - all reference types are nullable oblivious when annotation context is disabled.
    - will return warnings when a potential null value is assigned to it.
    - has a default null-state value of not-null.

- _nonnullable_
    - an unannotated reference type (a variable without the null operator `?`) is nonnullable when annotation context is enabled
    - will cause warnings if they are dereferenced in maybe-null state.
    - has a default null-state value of not-null.

- _nullable_
    - an annotated reference type (a variable with the null operator) is nullable, but a warning will be returned when the annotation context is disabled.
    - will return warnings when dereferenced in maybe-null state.
    - has a default null-state value of maybe-null.

### General strategy

- Address the warnings first
    - Start by enabling warnings only, and address these warnings one by one.
    - It’s better that all types are nullable oblivious first before turning on the type annotations which will generate another set of warnings.
    - Goal is to remove the warnings at the warnings context first before proceeding.

- Enable type annotations
    - What this does is it changes the reference types from oblivious to nonnullable.
    - Identify which variables, method parameters and return values are expecting null values or not.
    - The goal in this step is not only fix the warnings, but informing the compiler which variables should be monitored for null values and not.

- Use [[reflection-attributes#**Attributes**|attributes]] to extend type annotations
    - Meant for API request and response object validation as API have rules as to when a certain field has a null or not null value.

- Finally, set the project’s Nullable property to enabled
    - Remove any directives pertaining to disabling or enabling nullable features.
    - At this point, you can branch out to other code bases that act as dependency to the current project being migrated.