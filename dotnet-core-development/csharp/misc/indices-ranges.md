# Indices and Ranges
---

### Indices rules

Given the array sequence,

- The 0 index as the same as `sequence[0]`
- **End operator** - (^) reads as `sequence[sequence.Length]`
    - Usage: `sequence[^5]`
- **Range operator** - (..) reads as `sequence.Length - n`, where n is any number.

### Range rules

- Start value is inclusive - meaning the provided start value is included in the range
- End value is exclusive - the provided end value is not included in the range

`array[..4]` Get elements of an array until the third element. (end value is excluded from the result)

- Range and index operations can be saved on a variable of type `Range` and `Index` respectively.

### Span

- Represents “_a contiguous chunk of memory_” that resides in the managed heap, stack or unmanaged memory.
- Provides low level interfaces to array, string or any connected managed or unmanaged memory block
- Primarily used for micro optimizations, enables writing low allocation code that does not put a strain on the garbage collector.

NOTE: When taking a range from an array, the result is an array that is a copy of an original array - A result of a range stems follows copy semantic.