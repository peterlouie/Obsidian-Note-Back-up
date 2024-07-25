Time Created: 23 July 2024 22:50

Tags: #rust/smartpointer #rust/pointer #rust/Rc

## Smart Pointers

- Allow multiple owners of data
- Reference counted - ***"Rc"*** 
	- Data deleted only when last owner is dropped
- Atomic reference counted = ***"Arc"*** 
	- Safe to use with multiple threads

![[Smart Pointer.png]]

## Recap

- ***Rc & Arc*** are used to share ownership
- Data is dropped once all owner are dropped
- ***Rc*** for single-threading
	- ***Rc::clone*** to make a new reference
- ***Arc*** for multi-threading
	- ***Arc::clone*** to make a new reference
	