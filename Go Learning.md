### Tests
- when writing tests for a specific file, naming convention needs to be: `xxx_test.go`
	- Test function must start with the word test
	- function will only take in one argument, t (star)testing.T
- For the Hello() test we use the Errorf method. f means it formats the string with values inserted into the placeholder values such as %q
- 