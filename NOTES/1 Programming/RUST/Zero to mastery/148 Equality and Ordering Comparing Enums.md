Time Created: 26 July 2024 10:47

Tags: #rust/partialeq/enum #rust/partialord/enum

## Comparison Operators

- Enums can be compared using equality operators
- Derivable traits enable enums to be compared
- ***PartialEq*** 
	- Provides equality
- ***PartialOrd*** 
	- Provides ordering: greater/less than
- ***PartialOrd*** requires ***PartialEq*** to be implemented
- Usually don't need to manually implement

![[148 PartialEq Enum.png]]

![[148 PartialOrd Enum.png]]

![[148 PartialOrd Enum with Variant Data.png]]

## Recap

- Enums can be sorted and compared
	- ***PartialOrd*** and ***PartialEq*** implementation required
- These traits can be used with ***derive*** 
- Ordering respects enum variant order in the code
	- Variant data will only be considered for ordering if both enumerations are the <u>same variant</u> 
- Manual implementation almost never needed for enums

