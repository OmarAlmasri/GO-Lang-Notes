
```go title="Syntax Example"
func sub(x int, y int) int {
    return x-y
}
```

# Multiple Parameters

When multiple arguments are of the same type, and are next to each other in the function signature, the type only needs to be declared after the last argument.

```go
func addToDatabase(hp, damage int, name string, level int) {
  // ?
}
```

# Declaration Syntax

The [following post on the Go blog](https://blog.golang.org/declaration-syntax) is a great resource for further reading on declaration syntax.

# Ignoring Return Values

A function can return a value that the caller doesn't care about. We can explicitly ignore variables by using an underscore, or more precisely, the [blank identifier `_`](https://go.dev/doc/effective_go#blank).

For example:

```go
func getPoint() (x int, y int) {
    return 3, 4
}

// ignore y value
x, _ := getPoint()
```

Even though `getPoint()` returns two values, we can capture the first one and ignore the second.

# Named Return Values

Return values may be given names, and if they are, then they are treated the same as if they were new variables defined at the top of the function.

Named return values are best thought of as a way to document the purpose of the returned values.

> [!info]
> A return statement without arguments returns the named return values. This is known as a "**naked**" return. Naked return statements should be used only in short functions. They can harm readability in longer functions.


Named return values are what enable naked returns. Use naked returns only in short functions where the purpose of the returned values is obvious.

```go
func getCoords() (x, y int) {
	// x and y are initialized with zero values

	return // automatically returns x and y
}

```

Is the same as:

```go
func getCoords() (int, int) {
	var x int
	var y int
	return x, y
}
```

In the first example, `x` and `y` are the return values. At the end of the function, we could simply write `return` to return the values of those two variables, rather than writing `return x,y`.

# Functions As Values

Go supports sending functions as parameters of other functions.

```go
func aggregate(a, b, c int, arithmetic func(int, int) int) int {
  firstResult := arithmetic(a, b)
  secondResult := arithmetic(firstResult, c)
  return secondResult
}
```

In the past example it calls the `arithmetic` function and applies it to three inputs instead of two.

# Anonymous Functions

```go title="Code Example"
package main

import "fmt"

func printReports(intro, body, outro string) {
	printCostReport(func (intro string) int {
		return len(intro) * 2
	}, intro)
	printCostReport(func (body string) int {
		return len(body) * 3
	}, body)
	printCostReport(func (outro string) int {
		return len(outro) * 4
	}, outro)
}


func main() {
	printReports(
		"Welcome to the Hotel California",
		"Such a lovely place",
		"Plenty of room at the Hotel California",
	)
}

func printCostReport(costCalculator func(string) int, message string) {
	cost := costCalculator(message)
	fmt.Printf(`Message: "%s" Cost: %v cents`, message, cost)
	fmt.Println()
}

```

# Defer

`defer` can be used for multiple purposes

`defer` delays the execution of a function until the **surrounding function returns**.


```go
func main() {
    defer fmt.Println("I print last")
    fmt.Println("I print first")
    fmt.Println("I print second")
}

// Output:
// I print first
// I print second
// I print last
```

Deferred functions are typically used to clean up resources that are no longer being used. Often to close database connections, file handlers and the like.

```go
func GetUsername(dstName, srcName string) (username string, err error) {
	// Open a connection to a database
	conn, _ := db.Open(srcName)

	// Close the connection *anywhere* the GetUsername function returns
	defer conn.Close()

	username, err = db.FetchUser()
	if err != nil {
		// The defer statement is auto-executed if we return here
		return "", err
	}

	// The defer statement is auto-executed if we return here
	return username, nil
}
```

In the above example, the `conn.Close()` function is not called here:

```go
defer conn.Close()
```

It's called:

```go
// here
return "", err
// or here
return username, nil
```

## Multiple Defers

The location of a `defer` statement inside a function matters. The deferred call is registered at the point where `defer` is executed, and it will run when the function returns. If you have multiple `defer` statements in a single function, they are executed in **last-in, first-out** order (the last deferred call runs first).

For example, you'd want to close a file before trying to remove it:

```go
func CreateTempFile() {
	f, _ := os.Create("temp-42.txt")
	defer os.Remove(f.Name()) // executed second
	defer f.Close()           // executed first

	fmt.Fprintln(f, "How many roads must a man walk down?")
}
```

# Closures

A [closure](https://en.wikipedia.org/wiki/Closure_\(computer_programming\)) is a function that references variables from outside its own function body. The function may access and _assign_ to the referenced variables.

In this example, the `concatter()` function returns a function that has reference to an _enclosed_ `doc` value. Each successive call to `harryPotterAggregator` mutates that same `doc` variable.

```go
func concatter() func(string) string {
	doc := ""
	return func(word string) string {
		doc += word + " "
		return doc
	}
}

func main() {
	harryPotterAggregator := concatter()
	harryPotterAggregator("Mr.")
	harryPotterAggregator("and")
	harryPotterAggregator("Mrs.")
	harryPotterAggregator("Dursley")
	harryPotterAggregator("of")
	harryPotterAggregator("number")
	harryPotterAggregator("four,")
	harryPotterAggregator("Privet")

	fmt.Println(harryPotterAggregator("Drive"))
	// Mr. and Mrs. Dursley of number four, Privet Drive
}
```

```go title="Example"
package main

func adder() func(int) int {
	sum := 0

	return func (numb int) int {
		sum += numb
		return sum
	}
}
```

# Currying

 **What is it?**
Currying transforms a multi-argument function into a chain of single-argument functions. In Go, this is implemented via **function factories** — functions that return other functions, with arguments "locked in" via **closures**.

 **Key Rules**
- A function can **accept** another function as a parameter
- A function can **return** a new function
- The returned function **closes over** (remembers) variables from its parent scope

```go
package main

import "fmt"

// Factory: locks in a prefix, returns a ready-to-use logger
func getLogger(formatter func(string, string) string) func(string, string) {
    return func(first, second string) {
        fmt.Println(formatter(first, second))
    }
}

func colonDelimit(a, b string) string { return a + ": " + b }
func commaDelimit(a, b string) string { return a + ", " + b }

func main() {
    colonLogger := getLogger(colonDelimit)
    commaLogger := getLogger(commaDelimit)

    colonLogger("ERROR", "out of memory") // ERROR: out of memory
    commaLogger("ERROR", "out of memory") // ERROR, out of memory
}
```

## Mental Model

```
getLogger(colonDelimit)
    └──► returns ──► func(first, second string) {
                         fmt.Println(colonDelimit(first, second))
                     }          └─ locked in via closure
```

