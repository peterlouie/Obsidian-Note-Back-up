Time Created: 26 July 2024 20:22

Tags: #rust/overloading #rust/index

## Operator Overloading

- Operators can be overloaded for structs and enums
- Trait implementation required
- All overloadable operators are available in ***std::ops*** module
- Behavior should be consistent with the meaning of the operator
	- Adding should make something larger
	- Subtracting should make it smaller, etc

![[150 Overloading example1.png]]

![[150 Overloading example2.png]]

![[150 Overloading example3.png]]

![[150 Overloading Operators.png]]

![[150 Overloading example code.png]]

![[150 Overloading Implementation.png]]

>[!note] Note!
> "[]" is the Index operator

## Recap

- Operators are overloaded via traits
	- Listing of traits is in ***std::ops*** module
- Input type can be specified with generic parameter
- Output type can be specified with the ***Output*** associated type alias
- Behavior should remain consistent with operator purpose

