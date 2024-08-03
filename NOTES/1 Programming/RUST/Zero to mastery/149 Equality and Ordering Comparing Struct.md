Time Created: 26 July 2024 11:01

Tags: #rust/

## Comparison Operators

- Structs can be compared using equality operators
- Derivable traits enable structs to be compared
	- ***PartialEq***
		- Provides equality
	- ***PartialOrd***
		- Provides ordering: greate/less than
- ***PartialOrd*** requires ***PartialEq*** to be implemented

#### PartialEq Struct Example
```rust
#[derive(PartialEq)]
struct User {
	id: i32,
	name: String,
}

fn main() {
	let a = User { id: 1, name: "a".to_owned() };
	let b = User { id: 2, name: "b".to_owned() };

	if a == b {
		println!("equal")
	}else{
		println!("not equal")
	}

	//will print not equal
}
```

#### PartialOrd Struct Example
```rust
#[derive(PartialEq, PartialOrd)]
struct User {
	id: i32,
	name: String,
}

fn main() {
	let a = User { id: 1, name: "a".to_owned() };
	let b = User { id: 2, name: "b".to_owned() };

	if a < b {
		println!("less than")
	}else{
		println!("greater than")
	}

	//will print less than
}
```
## PartiaIOrd

- ***PartialOrd*** only considers the first struct field
- Manual implementation needed to compare other fields
	- Always ensure ***PartialOrd*** and ***PartialEq*** are consistent
### PartialOrd Struct Manual Implementation
 ```rust
use std::cmp::Ordering

#[derive(PartialEq)]
struct User {
	id: i32,
	name: String,
}

impl PartialOrd for User {
	fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
		if self.name < other.name {
			Some(Ordering::Less)
		} else if self.name > other.name {
			Some(Ordering::Greater)
		} else {
			Some(Ordering::Equal)
		}
	}
}
```

### PartialOrd Struct Manual Implementation
```rust
use std::cmp::Ordering

#[derive(PartialEq)]
struct User {
	id: i32,
	name: String,
}

impl PartialOrd for User {
	fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
		// .cmp() is made available through
		// #[derive(Ord)]
			// Automatically derived on primitive types
		Some(self.name.cmp(&other.name))
	}
}

```
## Recap

- Struct can be sorted and compared
	- ***PartialOrd*** and ***PartialEq*** implementation required
	- ***Ord*** implementation optional
- These traits can be used with drive
- Ordering respects only the <u>first</u> struct field when derived
	- Manual implementation required to order on different fields
	- Ordering and equality must remain consistent when implementing manually

