# 🚨 Numeric types

- All numeric types (and even strings) can be converted to their corresponding bytes, or byte array characters.

## Floating-point types

- `Half`, `Single`, `Double` and `Decimal`
- Intended to be used on values that require high degree of precision and a small rounding error is not required.
	- Commonly used for scenarios like measuring exact distances between two points
	- `Decimal` is used when rounding errors should minimized and a greater deal of precision is required.

## SIMD-Enabled Types

- SIMD is ***Single Instruction, Multiple Data***
- A technique where the same operation can be applied to multiple data simultaneously which increases computational efficiency.
	- Helpful for tasks that involves processing a huge amount of data that includes image/graphics and audio processing and numerical computations.
- What achieves this computational efficiency is that these operations can be run in parallel at the hardware level.

- SIMD-enabled types include:
	- Vectors
	- Plane
	- Quarternion

## Booleans

- When **converting** Boolean to integral values, <u>non-zero values convert to true</u> and <u>zero value converts to false</u>.
- When **parsing** Boolean values, the only acceptable value that can be parsed are the words, "True", "False", "true", "false" or a <u>mixed case</u> equivalent.

- Marshaling Boolean is a bit tricky compared to other base data types because it can be converted into three different integer value formats.
	- Marshaling refers to the process of converting a type or an object into a different format so that it can communicate with other software components, which are may be written in a different language.

## Bytes

- Bytes normally ==accept unsigned values from 0 to 255==, but it also has an ==8-bit integer value type== variant named as `SByte` which ==accepts values from -128 to 127==.

- It is possible to assign s non-byte numeric value to a byte but it needs to be casted.
	- If the numeric value has a floating point, it will remove the precision and get the integer value instead.

- When parsing byte values, a hexadecimal string can be provided and it will be converted to its byte value counterpart.

### Formatting

- Use the **D** format (usually followed with a positive integer value) in the `ToString` method to format the value with leading zeroes.
	- The number of leading zeroes depend on the integer value specified along with the D specifier.
- Use the **X** format to specify a byte as a hexadecimal string.
- Use the `ToString` method overload that accepts two method parameters with the final parameter being the value that tells whether a byte shall be converted to decimal, binary, octal or hexadecimal.

## Decimals

- Can hold more whole numbers (numbers at the left of the decimal point) and decimal numbers (numbers at the right of the decimal point) than `float` and `double`.
- This makes it perfect to be used for financial calculation where extreme precision without rounding off is necessary.

- **Behind the scenes**
	- The decimal type is a 128-bit value comprised of two parts:
		- 96-bit for the integer
		- 32-bit for the sign (positive or negative) and the scaling factor
			- The ***scaling factor*** indicates the position of the decimal point.
				- Also preserves the leading zeroes of the decimal which can be displayed by using the appropriate format string.

- **Conversions**
	- Decimals can be converted to `char`.
	- Decimals can also be converted to `Single` and `Double` types.
		- This is a narrowing conversion as `Single` and `Double` have lesser bit values compared to decimal.
		- Some decimal numbers may be lost as a result of the conversion.
	- Conversions that fail to express to a valid resulting value either as a decimal or other type will return an exception, one of which is `OverflowException`.

## Double

- 64-bit number that also includes positive or negative zero, `PositiveInfinity`, `NegativeInfinity`, and `NaN`.
- Used for extremely large or small values that are imprecise like distance between planets or atoms.

### Representation and precision

- Provides an imprecise representation of a value as it uses binary fraction to represent its floating values.
	- This means that when you operate on a floating value multiple times, it becomes less and less precise.
	- This is demonstrated by using the `R` numeric string format to display all 17 digits of precision supported by the double type.
- The imprecise nature of this double type can cause issues when comparing exact double value to a double value derived from calculating some numbers together.

- Another limitation of the double type is when an original value of double type is converted to another type and is converted again back to double, which is described as "*value round-trip*", did not preserve the original value as the conversion has lost precision (less significant digits) during the process.
	- Use the `G17` numeric string format to preserve the value of a double type variable during a round trip.
	- One major example of this scenario is when a round trip is made between the double type and the single type.
		- One way to resolve this issue is to round off the values after the conversion so that both the single and double values will have the same precision.

- The loss of precision for the double type is also affected by the platform where the code is ran.
	- Result of calculations may be different in 32-bit and 64-bit versions of .NET.

