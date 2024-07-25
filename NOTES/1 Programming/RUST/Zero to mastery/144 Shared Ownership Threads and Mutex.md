Time Created: 25 July 2024 21:28

Tags: #rust/arc #rust/mutex

## Shared Data w/ Threading

- Threads execute non-deterministically
	- Can read/write at random times
- Multiple threads can work with the same data
	- Data can become corrupted easily
		- Difficult to work with threads

![[Data Corruption.png]]

## Synchronization

- Data needs to be synchronized for safe access
- Common synchronization primitive is a ***Mutex***
	- Mutually Exclusive lock
- Uses atomic operations to ensure that data is only accessed by one thread at a time
	- Atomic operations are "all or nothing" operations, enforced by the CPU
		- Data stays consistent

## Mutex

- ***Mutexes*** wrap data, making data mutually exclusive
	- Only one thread can access at a time
	- All other threads will wait until finished
- ***Mutexes*** <u>cannot</u> be shared among threads
	- Wrap with a smart pointer (***Arc***) 
	- Share the ***Arc*** among threads
- Use ***parking_lot*** create for a ***Mutex***
	- Better API & performance than stdlib

![[How mutex works.png]]

![[Arc Mutex Sample 1.png]]

![[Arc Mutex Sample 2.png]]

## Recap

- Data access from threads must be synchronized
	- Wrap data in a ***Mutex*** 
	- Use ***.lock()*** to acquire a lock
	- Unlocking occurs when the lock is dropped
- ***Mutexes*** cannot be shared
	- Wrap in ***Arc*** to share between threads
- Lock a minimum amount of time by performing computations <u>before</u> taking a lock

