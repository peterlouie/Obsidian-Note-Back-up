Time Created: 23 July 2024 12:17

Tags: #rust/channels #rust/channel/bidirection

# Channels

- One-way communication between threads
	- Message passing
	- ***Sender*** and ***Reciever***
- Can have limited or unlimited capacity
- ***crossbeam-channel*** create
	- Use docs.rs website to view documentation for crates
```yaml
[dependencies]
crossbeam-channel = '*'
```

## Message Passing

- *enum* commonly used for messages
	- **match** allows easy message handling
- Guaranteed in-order delivery
- Can be blocking or non-blocking
	- Block on *Sender*: Channel full
	- Block on *Receiver*: No messages
	- Behavior determined by function, not by channel

## Demo
```rust
use crossbeam_channel::unbounded;
use std::thread;

enum ThreadMsg {
    PrintData(String),
    Sum(i64, i64),
    Quit,
}

fn main() {
    let (s, r) = unbounded();
    let handle = thread::spawn(move || loop {
        match r.recv() {
            Ok(msg) => match msg {
                ThreadMsg::PrintData(d) => println!("{}", d),
                ThreadMsg::Sum(lhs, rhs) => println!("{}+{}={}", lhs, rhs, (lhs + rhs)),
                ThreadMsg::Quit => {
                    println!("thread terminating");
                    break;
                }
            },
            Err(e) => {
                println!("disconnected {:?}", e);
                break;
            }
        }
    });

    s.send(ThreadMsg::PrintData("Hello from main".to_owned()));
    s.send(ThreadMsg::Sum(2, 2));
    //s.send(ThreadMsg::Quit);
	drop(s)

    handle.join();
}
```

## Demo bidirectional
```rust
use crossbeam_channel::unbounded;
use std::thread;

enum WorkerMsg {
    PrintData(String),
    Sum(i64, i64),
    Quit,
}

enum MainMsg {
    SumResult(i64),
    WorkerQuit,
}

fn main() {
    let (worker_tx, worker_rx) = unbounded();
    let (main_tx, main_rx) = unbounded();

    //let (s, r) = unbounded();
    let worker = thread::spawn(move || loop {
        match worker_rx.recv() {
            Ok(msg) => match msg {
                WorkerMsg::PrintData(d) => println!("Worker: {}", d),
                WorkerMsg::Sum(lhs, rhs) => {
                    println!("Worker: summing...");
                    main_tx.send(MainMsg::SumResult(lhs + rhs));
                    ()
                }
                WorkerMsg::Quit => {
                    println!("Worker: terminating");
                    main_tx.send(MainMsg::WorkerQuit);
                    break;
                }
            },
            Err(e) => {
                println!("Worker: disconnected {:?}", e);
                main_tx.try_send(MainMsg::WorkerQuit);
                break;
            }
        }
    });

    worker_tx.send(WorkerMsg::PrintData("Hello from main".to_owned()));
    worker_tx.send(WorkerMsg::Sum(2, 2));
    worker_tx.send(WorkerMsg::Quit);

    while let Ok(msg) = main_rx.recv() {
        match msg {
            MainMsg::SumResult(answer) => println!("Main: answer = {}", answer),
            MainMsg::WorkerQuit => println!("Main: worker terminated"),
        }
    }

    worker.join();
}
```

## Activity
```rust
// Topic: Channels
//
// Summary:
//   Using the existing code, create a program that simulates an internet-of-things
//   remote control light bulb. The color of the light can be changed remotely.
//   Use threads and channels to communicate what color the light bulb should display.
//
// Requirements:
// * Create a separate thread representing the light bulb
// * Use a channel to communicate with the thread
// * Display a color change message using the println! macro
// * The light bulb must also be able to turn on and off
//   * Display whether the light is on or off on each color change
// * Turn off the light when disconnecting from it
//
// Notes:
// * Remember to add `crossbeam-channel` to your Cargo.toml file
// * Use the `colored` crate if you want to get fancy and display actual colors
// * The docs.rs site can be used to read documentation for third-party crates
// * Disconnection can be accomplished by dropping the sender, or
//   by telling the thread to self-terminate
// * Use `cargo test --bin a39` to test your program to ensure all cases are covered

use colored::Colorize;
use crossbeam_channel::{unbounded, Receiver};
use std::thread::{self, JoinHandle};

enum LightMsg {
    // Add additional variants needed to complete the exercise
    ChangeColor(u8, u8, u8),
    Disconnect,
    Off,
    On,
}

enum LightStatus {
    Off,
    On,
}

fn spawn_light_thread(receiver: Receiver<LightMsg>) -> JoinHandle<LightStatus> {
    // Add code here to spawn a thread to control the light bulb
    thread::spawn(move || {
        let mut light_status = LightStatus::Off;
        loop {
            if let Ok(msg) = receiver.recv() {
                match msg {
                    LightMsg::ChangeColor(r, g, b) => {
                        println!("Color changed to: {}", "       ".on_truecolor(r, g, b));
                        match light_status {
                            LightStatus::Off => println!("Light is off"),
                            LightStatus::On => println!("Light is on"),
                        }
                    }
                    LightMsg::On => {
                        println!("Turn light on");
                        light_status = LightStatus::On;
                    }
                    LightMsg::Off => {
                        println!("Turn light off");
                        light_status = LightStatus::Off;
                    }
                    LightMsg::Disconnect => {
                        println!("light disconnected");
                        light_status = LightStatus::Off;
                        break;
                    }
                }
            } else {
                println!("channel disconnected");
                light_status = LightStatus::Off;
                break;
            }
        }
        light_status
    })
}

fn main() {
    let (s, r) = unbounded();

    let light = spawn_light_thread(r);

    s.send(LightMsg::On);
    s.send(LightMsg::ChangeColor(255, 0, 0));
    s.send(LightMsg::ChangeColor(0, 128, 0));
    s.send(LightMsg::ChangeColor(0, 0, 255));
    s.send(LightMsg::Off);
    s.send(LightMsg::Disconnect);

    let light_status = light.join();
}

#[cfg(test)]
mod test {
    use super::*;
    use crossbeam_channel::unbounded;

    #[test]
    fn light_off_when_disconnect() {
        let (s, r) = unbounded();

        let light = spawn_light_thread(r);
        s.send(LightMsg::Disconnect).expect("channel disconnected");

        let light_status = light.join().expect("failed to join light thread");

        if let LightStatus::On = light_status {
            panic!("light should be off after disconnection");
        }
    }

    #[test]
    fn light_off_when_dropped() {
        let (s, r) = unbounded();

        let light = spawn_light_thread(r);
        drop(s);

        let light_status = light.join().expect("failed to join light thread");

        if let LightStatus::On = light_status {
            panic!("light should be off after dropping sender");
        }
    }
}
```


