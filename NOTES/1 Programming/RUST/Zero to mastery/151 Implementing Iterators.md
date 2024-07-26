Time Created: 26 July 2024 22:42

Tags: #rust/

## Iterator

- Iteration is provided by the ***Iterator*** trait
	- Only one function to be implemented
	- Provides ***for..in*** syntax
	- Access to all iterator adapters
		- ***map, take, filter,*** etc
- Can be implemented for any structure

![[151 Iterator Trait.png]]

![[151 Iterator Example.png]]

![[151 Iterator code.png]]

![[151 Iterators for..in.png]]

## Recap

- Implementing ***Iterator*** provides access to ***for..in*** syntax and iterator adapters
	- Set the output type using the ***Item*** associated type as part of the ***Iterator*** trait
	- Return ***Some*** when data is available and ***None*** when there are no more items to iterate
- Data structures must:
	- Be mutable
	- Have field to track iteration

