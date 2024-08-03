Time Created: 26 July 2024 20:22

Tags: #rust/overloading #rust/index

## Operator Overloading

- Operators can be overloaded for structs and enums
- Trait implementation required
- All overloadable operators are available in ***std::ops*** module
- Behavior should be consistent with the meaning of the operator
	- Adding should make something larger
	- Subtracting should make it smaller, etc

```rust
use std::ops::Add;

struct Speed(u32);

impl Add<Self> for Speed {
	type Output = Self;

	fn add(self, rhs: Self) -> Self::Output {
		Speed(self.0 + rhs.0)
	}
}

fn main() {
	let fast = Speed(5) + Speed(3);
}
```

```rust
use std::ops::Add;

struct Speed(u32);

impl Add<Self> for Speed {
	type Output = Self;

	fn add(self, rhs: Self) -> Self::Output {
		Speed(self.0 + rhs.0)
	}
}

fn main() {
	let fast = Speed(5) + 3;
}
```

```rust
use std::ops::Add;

struct Letter(char);

impl Add<Self> for Letter{
	type Output = String;

	fn add(self, rhs: Self) -> Self::Output {
		format!("{}{}", self.0, rhs.0)
	}
}

fn main() {
	println!("{}", Letter('h') + Letter('i'));
}
```

![[150 Overloading Operators.png]]

### Overloading Example Code
```rust
use std::ops::Index;

enum Temp {
	Current,
	Max,
	Min
}

impl Index<Temp> for Hvac {
	type Output = i16;
	fn index(&self, temp: Temp) -> &Self::Output {
		match temp {
			Temp::Current => &self.current_temp,
			Temp::Max => &self.max_temp,
			Temp::Min => %self.min_temp,
		}
	}
}

struct Hvac {
	current_temp: i16,
	max_temp: i16,
	min_temp: i16,
}

fn main() {
	let env = Hvac {
		current_temp: 30,
		max_temp: 60,
		min_temp: 0
	};

	let current = env[Temp::Current];
}

```

>[!note] Note!
> "[]" is the Index operator

## Recap

- Operators are overloaded via traits
	- Listing of traits is in ***std::ops*** module
- Input type can be specified with generic parameter
- Output type can be specified with the ***Output*** associated type alias
- Behavior should remain consistent with operator purpose

