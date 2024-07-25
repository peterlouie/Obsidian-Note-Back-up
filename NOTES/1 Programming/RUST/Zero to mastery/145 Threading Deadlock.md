Time Created: 25 July 2024 22:07

Tags: #rust/

## Deadlock

- A deadlock is a situation where locks are waiting on one another
	- Threads become "stuck" and are unable to continue
- Deadlocks can occur when:
	- Using multiple locks
	- Recursing while taking a lock
	- Locking the same lock twice

![[Recursive Deadlock Example.png]]

![[Fix Deadlock.png]]

![[Threaded Deadlock Example.png]]

![[Fixing Threaded Deadlock.png]]

![[Threaded Contention.png]]

## Recap

- Deadlocks are permanently stuck locks
- ***ReentrantMutex*** allows multiple locks from the same thread
	- Use for recursive functions
	- Anytime you need to lock the <u>same lock</u> more than once
- ***try_lock()*** can prevent deadlocks
	- Drop <u>all locks</u> used in function and try again after a short period
		- Use the ***backoff*** create for optimal performance

