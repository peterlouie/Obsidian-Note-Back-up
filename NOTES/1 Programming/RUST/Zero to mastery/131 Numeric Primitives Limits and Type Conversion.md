Time Created: 23 July 2024 09:31

Tags: #rust/signed #rust/unsigned #rust/conversion

## Many Numeric Types

- 8, 16, 32, 64, and 128 bit integers
	- Signed & unsigned
- *isize* & *usize* 
	- Pointer sized numeric types
		- usize used to index into arrays
	- Depeds on architecture: 16bit, 64bit, etc
- 32bit & 64bit floating point

### Unsigned Integer
![[Unsigned Integer.png]]

### Signed Integer
![[SIgned Integer.png]]

### Literal Numeric Annotations
![[Literal Numeric Annotations.png]]

### Conversion

- Integers can be converted between types
	- *u8* will always fit into a u16
		- Lossless conversion
	- u16 connot fit into u8, but it can still be converted
		- Value will be a number in the range of the target type
- Math operations require all operands to be the same type
	- Convert to the largest type needed

## Cast Syntax
```rust
let a = 15u8 as u16;
let b = a as u8 + 20u16 as u8;
```

### Casting to less bits
- (Source value) - (Target max + 1)
	- Repeat until the value fits in the type
- Alternatively: (Source value) *modulus* (Target max + 1)
- This happens automatically when using *as* to convert
![[Casting to less bits.png]]

### Converting Floats to Integer
- Float to integer is a *saturating conversion*
	- The value will be clamped to the minimum or maximum of the target type
- Decimal points are truncated/dropped
![[floats to integer.png]]

### Checked Casting
```rust
u8::try_from(300u16)
```

# Recap

- Numeric types can be cast using the *as* keyword
- Use ***TryFrom*** when you want to be sure the value will properly fit
- Annotations can be used with numeric literals to specify the type
	- Can use underscore __ as a digit seperator
- Compiler error to create a numeric literal outside of appropriate range