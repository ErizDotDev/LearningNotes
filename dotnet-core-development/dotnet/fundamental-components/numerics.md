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