Time Created: 25 July 2024 12:07

Tags: #rust/mutability #rust/cell #rust/refcell 

## Interior Mutability

- Mutable data is sometimes problematic
	- Compiler errors, ownership issues, etc
- Possible to create permanently mutable memory
	- Less restrictive than compiler
		- Trade-offs in implementation & performance

---
### Cell

- Permanently mutable memory location
	- Can always be mutated, even if the containing structure is immutable
- Accessing **Cell** data always results in a <u>move</u> or <u>copy</u> 
- Data should be copy-able
	- #[derive(Clone, Copy)]
	- Inefficient for large data types
		- Limit to numbers and booleans
- Prefer ***mut*** 

#### Cell
```rust
use std::cell::Cell;

#[derive(Debug)]
struct Book {
	signed: Cell<bool>,
}

impl Book {
	fn sign(&self) {
		self.signed.set(true);
	}
	fn signed(&self) -> bool {
		self.signed.get()
	}
}

fn main(){
	let my_book = Book {
		signed: Cell::new(false),
	};

	println!("signed: {}", my_book.signed()); // signed: false
	my_book.sign();
	println!("signed: {}", my_book.signed()); // signed: true
}
```

---

### RefCell

- Permanently mutable memory location
	- Can always be mutated, even if the containing structure is immutable
- Accessing ***RefCell*** data always results in a <u>borrow</u> 
	- Efficient data access (compared to ***Cell***)
	- Borrow checked at runtime
		- Will panic at runtime if rules are broken
		- Only one mutable borrow at a time
- Prefer ***&mut*** 
- Not thread-safe

#### RefCell Borrow
```rust
use std::cell::RefCell;

struct Person {
	name: RefCell<String>,
}

fn main(){
	let name = "Amy".to_owned();

	let person = Person {
		name: RefCell::new(name)
	};

	let name = person.name.borrow();

	println!("{}", name) // Amy

}
```
	
#### Mutation
```rust
use std::cell::RefCell;

struct Person {
	name: RefCell<String>,
}

fn main() {
	let name = "Amny".to_owned();
	let person = Person {
		name: RefCell::new(name),
	};

	{
		let mut name = person.name.borrow_mut();
		*name = "Tim".to_owned();
	};

	{
		person.name.replace("Tim".to_owned());
	};

}

// println! does not work here use dbg!
```

>[!note] Note!
>Using curly bracket can remove panic error or use checked borrow
#### Checked Borrow
```rust
use std::cell::RefCell;

struct Person {
	name: RefCell<String>,
}

fn main() {
	let name = "Amny".to_owned();
	let person = Person {
		name: RefCell::new(name),
	};

	let name: Result<_, _> = person.name.try_borrow();
	let name: Result<_, _> = person.name.try_borrow_mut();
}

```
### Recap

- ***Cell & RefCell*** allow permanent mutation
	- ***Cell*** returns owned data
	- ***RefCell*** returns borrowed data
- ***RefCell*** borrowing can panic at runtime
	- ***try_borrow*** and ***try_borrow_mut*** are non-panicking version
- Prefer to use ***mut*** and ***&mut*** 
	- Use ***Cell & RefCell*** only when it's not possibe to express intentions otherwise
- Not thread-safe

---
### Demo

```rust
use std::{cell::RefCell, rc::Rc};

#[derive(Debug)]
enum MenuItem {
    Drink,
    Salad,
}

#[derive(Debug)]
struct ItemOrder {
    item: MenuItem,
    quantity: u32,
}

#[derive(Debug)]
struct TableOrder {
    items: Vec<ItemOrder>,
}

fn new_table_order() -> TableOrder {
    TableOrder {
        items: vec![ItemOrder {
            item: MenuItem::Drink,
            quantity: 1,
        }],
    }
}

type Order = Rc<RefCell<Vec<TableOrder>>>;

#[derive(Debug)]
struct Chef(Order);

#[derive(Debug)]
struct WaitStaff(Order);

#[derive(Debug)]
struct Accounting(Order);

fn main() {
    let orders = Rc::new(RefCell::new(vec![]));

    let chef = Chef(Rc::clone(&orders));
    let wait_staff = WaitStaff(Rc::clone(&orders));
    let account = Accounting(Rc::clone(&orders));

    let order = new_table_order();

    {
        orders.borrow_mut().push(order);
    }

    dbg!(chef.0.borrow());
    dbg!(wait_staff.0.borrow());
    dbg!(account.0.borrow());
}
```

