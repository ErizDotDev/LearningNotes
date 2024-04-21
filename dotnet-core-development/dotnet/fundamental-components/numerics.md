# 🚨 Numeric types

### Floating-point types

- `Half`, `Single`, `Double` and `Decimal`
- Intended to be used on values that require high degree of precision and a small rounding error is not required.
	- Commonly used for scenarios like measuring exact distances between two points
	- `Decimal` is used when rounding errors should minimized and a greater deal of precision is required.

### SIMD-Enabled Types

- SIMD is ***Single Instruction, Multiple Data***
- A technique where the same operation can be applied to multiple data simultaneously which increases computational efficiency.
	- Helpful for tasks that involves processing a huge amount of data that includes image/graphics and audio processing and numerical computations.
- What achieves this computational efficiency is that these operations can be run in parallel at the hardware level.

- SIMD-enabled types include:
	- Vectors
	- Plane
	- Quarternion

### Booleans

- When **converting** Boolean to integral values, <u>non-zero values convert to true</u> and <u>zero value converts to false</u>.
- When **parsing** Boolean values, the only acceptable value that can be parsed are the words, "True", "False", "true", "false" or a <u>mixed case</u> equivalent.
- Marshaling Boolean is a bit tricky compared to other base data types because it can be converted into three different integer value formats.
	- Marshaling refers to the process of converting a type or an object into a different format so that it can communicate with other software components, which are may be written in a different language.