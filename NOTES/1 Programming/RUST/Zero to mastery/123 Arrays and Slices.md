Time Created: 22 July 2024 18:40

Tags: #rust/array #rust/slices

# Arrays

- Contiguous memory region
- All elements have the same size
- Array are not dynamically sized
	- Size must be hard-coded
	- Usually prefer Vector
- Useful when writing algorithms with a fixed buffer size
	- Networking, crypto, matrices

# Slices

- A borrowed view into an array
- Can be iterated upon
- Optionally mutable
- Indices bounded by the slice size
	- Cannot go out of bounds of the initial slice
- Can create any number of subslices from an existing slice

![[123 Slices - View Into An Array.png]]

# Slices & Vectors

- Borrowing a Vector as an argument to a function that requires a slice will automatically obtain a slice
- Always prefer to borrow a slice instead of a Vector

```rust
fn func(slice: &[u8]){}

let number = vec![1,2,3]
func(&number);
let number: &[u8] = numbers.as_slice();
```

![[123 Slicing with ranges.png]]

First example is inclusive range
Second example is exclusive range

# Recap

- Arrays must be statically initialized with hard-coded lengths
- Slices are a way to access parts of an array
- Array-backed data structures like Vectors can be sliced
- Slice lengths are always bound by the size of the slice
- Subslices can be created from existing slices

==============================================

# Slice Pattern

## Use Case

- Read the first few bytes to determine header information
	- Take different actions based on the data using match
- Get the first or last elements of a slice
- No need for bounds checking on slices
	- Compiler ensures access are always within bounds

## Overlapping Patterns

- Patterns easily overlap
- Minimize number of match arms to avoid bugs

## Prevent Overlapping Patterns

- Match the largest patterns first, followed by smaller patterns

## Recap

- Slices can be matched on specific patterns
	- These patterns can include match guards
- Match on largest patterns first, followed by smaller patterns
	- Smaller patterns tend to be greedy
- Minimize the number of match arms to avoid bugs

# Activity

```rust
// Topic: Arrays & Slices
//
// Requirements:
// * Print pairs of numbers and their sums as they are streamed from a data source
// * If only one number is received, then print "Unpaired value: V",
//   where V is the value
// * If no numbers are received, print "Data stream complete"
//
// Notes:
// * A simulated data stream is already configured in the code
// * See the stdlib docs for the "chunks" method on "slice" for more info

fn data() -> &'static [u64] {
    &[5, 5, 4, 4, 3, 3, 1]
}

fn process_chunk(data: &[u64]) {
    match data {
        [lhs, rhs] => println!("{} + {} = {}", lhs, rhs, (lhs + rhs)),
        [single] => println!("Unpaired: {}", single),
        [] => println!("Data stream complete"),
        [..] => unreachable!("chunck size should be at 2 element"),
    }
}

fn main() {
    // `stream` is an iterator of Option<&[u64]>
    let stream = data().chunks(2);

    for chunk in stream {
        process_chunk(chunk);
    }
}
```

>[!note] Note!
>*unreachable!* keyword means it should not arrive on this code of line

