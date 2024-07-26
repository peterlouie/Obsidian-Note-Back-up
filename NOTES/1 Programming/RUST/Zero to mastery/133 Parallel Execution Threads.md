Time Created: 23 July 2024 10:28

Tags: #rust/thread

# Thread Basics

- A thread uses serial execution
	- Each line of code is  executed one at a time
- Multicore CPUs can have mulitple threads
	- Threads still executes serially
	- Each thread can execute different tasks
		- Better CPU utilization
- Threads are isolated from one another
	- Require additional work to communicate
		- Should communicate infrequently for performance reasons

# Working With Threads

- Threads are "spawned" (created)
	- Threads can spawn threads
	- Use the "main" thread for spawning in most cases
		- ***fn main()*** is the main thread
- Code is no longer executed line-by-line with threads
	- Requires careful planning
- When a thread completes work, it should be "joined" back into the main thread
	- Ensures that the thread has completed

### Non deterministic behavior
![[133 Thread Execution.png]]

![[133 Thread Lifetime.png]]

## Thread Memory

- Threads have "thread-local" memory
	- Owned by the thread
	- Only accessible in the thread
- Data can be copied or moved into threads
	- Can be done when thread created
	- Become thread-local

### Spawning a Thread
```rust
use std::thread;

let handle = thread::spawn(move || {
	// .. code ..
});

handle.join();
```

## Recap

- Threads are non-deterministic
	- Execution order will vary each time the program runs
- Ending the main thread will terminate all spawned threads
	- ***Join*** on the main thread to wait for threads to complete
- Each thread has it's own chunk of memory

# Demo

this will nondeterministic result
```rust
use std::thread;

fn main() {
    let iterations = 10;

    let a = thread::spawn(move || {
        for i in 1..=iterations {
            println!("A:{}", i)
        }
    });

    let b = thread::spawn(move || {
        for i in 1..=iterations {
            println!("   B:{}", i)
        }
    });

    a.join();
    b.join();
}
```

single thread with sleep time
```rust
use std::thread::{self, JoinHandle};
use std::time::Duration;

fn main() {
    let value: JoinHandle<usize> = thread::spawn(move || {
        thread::sleep(Duration::from_secs(1));
        42
    });

    println!("Waiting on thread");

    match value.join() {
        Ok(n) => println!("value: {}", n),
        Err(e) => println!("error joining thread: {:?} ", e),
    }
}
```

thread iteration on vector
```rust
use std::thread;

fn main() {
    let data = vec!['a', 'b', 'c'];
    let caps = thread::spawn(move || {
        let data: Vec<_> = data.iter().map(|c| c.to_ascii_uppercase()).collect();
        data
    });

    println!("Waiting for value...");

    match caps.join() {
        Ok(n) => println!("value: {:?}", n),
        Err(e) => println!("error: {:?}", e),
    }
}
```


# Activity

```rust
// Topic: Multithreading
//
// Requirements:
// * Run the provided functions in threads
// * Retrieve the data from the threads to print the message
//   "Hello, threads!"
//
// Notes:
// * Use the join function to wait for threads to finish

fn msg_hello() -> &'static str {
    use std::time::Duration;
    std::thread::sleep(Duration::from_millis(1000));
    "Hello, "
}

fn msg_thread() -> &'static str {
    use std::time::Duration;
    std::thread::sleep(Duration::from_millis(1000));
    "threads"
}

fn msg_excited() -> &'static str {
    use std::time::Duration;
    std::thread::sleep(Duration::from_millis(1000));
    "!"
}

fn main() {

    use std::thread;

    let msg_one = thread::spawn(move || msg_hello());
    let msg_two = thread::spawn(move || msg_thread());
    let msg_three = thread::spawn(move || msg_excited());

    let msg_one = msg_one.join().expect("failed join msg one");
    let msg_two = msg_two.join().expect("failed join msg two");
    let msg_three = msg_three.join().expect("failed join msg three");

    println!("{:?} {:?} {:?}", msg_one, msg_two, msg_three);
}
```

