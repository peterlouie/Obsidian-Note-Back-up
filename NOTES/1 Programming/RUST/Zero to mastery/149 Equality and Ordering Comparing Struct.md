Time Created: 26 July 2024 11:01

Tags: #rust/

## Comparison Operators

- Structs can be compared using equality operators
- Derivable traits enable structs to be compared
	- ***PartialEq***
		- Provides equality
	- ***PartialOrd***
		- Provides ordering: greate/less than
- ***PartialOrd*** requires ***PartialEq*** to be implemented

![[149 PartialEq Struct.png]]

![[149 PartialOrd Struct.png]]

## PartiaIOrd

- ***PartialOrd*** only considers the first struct field
- Manual implementation needed to compare other fields
	- Always ensure ***PartialOrd*** and ***PartialEq*** are consistent

![[149 PartialOrd Struct Manual Implementation1.png]]

![[149 PartialOrd Struct Manual Implementation2.png]]

## Recap

- Struct can be sorted and compared
	- ***PartialOrd*** and ***PartialEq*** implementation required
	- ***Ord*** implementation optional
- These traits can be used with drive
- Ordering respects only the <u>first</u> struct field when derived
	- Manual implementation required to order on different fields
	- Ordering and equality must remain consistent when implementing manually

