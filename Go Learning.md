### Tests
- when writing tests for a specific file, naming convention needs to be: `xxx_test.go`
	- Test function must start with the word test
	- function will only take in one argument, t (star)testing.T
- For the Hello() test we use the Errorf method. f means it formats the string with values inserted into the placeholder values such as %q

### Interesting Notes
- In go, there is only the keyword FOR on loops, no while, until, do etc etc
- Strings in go, like Java, are immutable
	- meaning every time we do a change/concatenationto it will copy over memory to accommodate the new string. this will ehavily impact performance 