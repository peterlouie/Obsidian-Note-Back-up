Time Created: 26 July 2024 22:42

Tags: #rust/

## Iterator

- Iteration is provided by the ***Iterator*** trait
	- Only one function to be implemented
	- Provides ***for..in*** syntax
	- Access to all iterator adapters
		- ***map, take, filter,*** etc
- Can be implemented for any structure
### Iterator Trait
```rust
trait Iterator {
	type Item;
	fn next(&mut self) -> Option<Self::Item>;
}
```
### Iterator Example
```rust
struct Odd {
	number: isize,
	max: isize,
}

impl Iterator for Odd {
	type Item = isize;

	fn next(&mut self) -> Option<Self::Item> {
		self.number += 2;
		if self.number <= self.max {
			Some(self.number)
		} else {
			None
		}
	}
}
```

### Iterator Example
```rust
impl Odd {
	fn new(max: isize) -> Self {
		Self { number: -1, max}
	}
}

let mut odds = Odd::new(7);

println!("{:?}", odds.next());
println!("{:?}", odds.next());
println!("{:?}", odds.next());
println!("{:?}", odds.next());
println!("{:?}", odds.next());

// Some(1)
// Some(3)
// Some(5)
// Some(7)
// None
```

 ```rust
let mut odds = Odd::new(7);

for o in odds {
	println!("odd: {}", o);
}

// odd: 1
// odd: 3
// odd: 5
// odd: 7
```

## Recap

- Implementing ***Iterator*** provides access to ***for..in*** syntax and iterator adapters
	- Set the output type using the ***Item*** associated type as part of the ***Iterator*** trait
	- Return ***Some*** when data is available and ***None*** when there are no more items to iterate
- Data structures must:
	- Be mutable
	- Have field to track iteration

