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

![[Cell.png]]

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

![[RefCell Borrow.png]]

![[RefCell Mutation.png]]

>[!note] Note!
>Using curly bracket can remove panic error or use checked borrow

![[RefCell checked borrow.png]]

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

