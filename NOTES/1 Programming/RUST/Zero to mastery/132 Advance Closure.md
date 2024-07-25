Time Created: 23 July 2024 10:02

Tags: #rust/closure

calling closure as function parameter

```rust
// this is the closure Fn(i32, i32) -> i32>
fn math(a: i32, b: i32, op: Box<dyn Fn(i32, i32) -> i32>) -> i32 {
    op(a, b)
}

fn main() {
    // let add = |a, b| a + b;
    // let add: Box<_> = Box::new(add);

    // println!("{}", math(2, 2, add))
    let add = Box::new(|a, b| {
        println!("adding");
        a + b
    });

    let sub = Box::new(|a, b| a - b);
    let mul = Box::new(|a, b| a * b);

    println!("{}", math(2, 2, add));
    println!("{}", math(2, 2, sub));
    println!("{}", math(2, 2, mul));
}
```

