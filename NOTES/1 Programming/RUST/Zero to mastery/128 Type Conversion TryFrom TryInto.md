Time Created: 22 July 2024 21:59

Tags: #rust/tryfrom #rust/tryinto

# TryFrom / TryInto

- *Fallible* type conversion
	- Use when there is the possibility of failure
- Just like *From/Into*, except it returns a *Result*
	- *TryFrom* will auto-implement *TryInto*
	