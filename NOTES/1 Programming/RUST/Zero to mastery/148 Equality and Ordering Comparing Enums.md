Time Created: 26 July 2024 10:47

Tags: #rust/partialeq/enum #rust/partialord/enum

## Comparison Operators

- Enums can be compared using equality operators
- Derivable traits enable enums to be compared
- ***PartialEq*** 
	- Provides equality
- ***PartialOrd*** 
	- Provides ordering: greater/less than
- ***PartialOrd*** requires ***PartialEq*** to be implemented
- Usually don't need to manually implement

#### PartialEq Enum Example
```rust
#[derive(PartialEq)]
enum Floor {
	ClientServices,
	Marketing,
	Ops,
}

fn main() {
	let first = Floor::ClientServices;
	let second = Floor::Marketing;

	if first == second {
		println!("Equal")
	}else {
		println!("Not Equal")
	}

	//will print Not Equal
}
```

#### PartialOrd Enum Example
```rust
#[derive(PartialEq, PartialOrd)]
enum Floor {
	ClientServices,
	Marketing,
	Ops,
}

fn is_below(this: &Floor, other: &Floor) -> bool {
	this < other
}

fn main() {
	let first = Floor::ClientServices;
	let second = Floor::Marketing;

	println!("{}", is_below(&first, &second)) //true
}
```

### PartialOrd Enum w/ Variant Data
```rust
#[derive(Debug, PartialEq, PartialOrd)]
enum Tax {
	Flat(f64),
	None,
	Percentage(f64),
}

fn smallest_ammount(tax: Tax, other: Tax) -> Tax {
	if tax < other {
		tax
	} else {
		other
	}
}

fn main(){
	let no_tax = Tax::None;
	let flat_tax = Tax::Flat(5.5);

	let finaltax = smallest_ammount(no_tax, flat_tax);
	println!("{:?}", finaltax) //Flat(5.5)
}
```
## Recap

- Enums can be sorted and compared
	- ***PartialOrd*** and ***PartialEq*** implementation required
- These traits can be used with ***derive*** 
- Ordering respects enum variant order in the code
	- Variant data will only be considered for ordering if both enumerations are the <u>same variant</u> 
- Manual implementation almost never needed for enums

