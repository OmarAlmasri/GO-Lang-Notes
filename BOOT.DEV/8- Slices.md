# Arrays in Go

**Declare an array:**

```go
var myInts [10]int
```

**Declare an initialized array:**

```go
primes := [6]int{2, 3, 5, 7, 11, 13}
```

# Slices in Go

A slice is a dynamically-sized, flexible view of the elements of an array.

The zero value of a slice is `nil`
Non-nil slices **always** have an underlying array, though it isn't always specified explicitly. To explicitly create a slice on top of an array we can do:

```go
primes := [6]int{2, 3, 5, 7, 11, 13}
mySlice := primes[1:4]
// mySlice = {3, 5, 7}
```

The syntax is:

```go
arrayname[lowIndex:highIndex]
arrayname[lowIndex:]
arrayname[:highIndex]
arrayname[:]
```

```go title="Slice Syntax"
age := []int{}
```

# Make

Most of time we don't need to think about the underlying array of a slice. We can create a slice using the `make` function:

```go
// func make([]T, len, cap) []T
mySlice := make([]int, 5, 10)

// the capacity argument is usually omitted and defaults to the length
mySlice := make([]int, 5)
```

Slices created with `make` will be filled with the zero value of the type.

If we want to create a slice with a specific set of values, we can use a slice literal:

```go
mySlice := []string{"I", "love", "go"}
// Notice the square brackets _do not_ have a `3` in them. If they did, you'd have an _array_ instead of a slice.
```

## Length

The length of the slice is the number of elements it contains

```go
mySlice := []string{"I", "Love", "Go"}
fmt.Println(len(mySlice)) // 3
```

## Capacity

The capacity of a slice is the number of elements in the underlying array, counting from the first element in the slice. It is accessed using the built-in `cap()` function:

```go
mySlice := []string{"I", "love", "go"}
fmt.Println(cap(mySlice)) // 3
```

Generally speaking, unless you're hyper-optimizing the memory usage of your program, you don't need to worry about the capacity of a slice because it will automatically grow as needed.

## Indexing

A programming concept you should already be familiar with is [array indexing](https://go.dev/blog/slices-intro#arrays). You can access or assign a single element of an array or slice by using its [index](https://en.wikipedia.org/wiki/Zero-based_numbering).

```go
mySlice := []string{"I", "love", "go"}
fmt.Println(mySlice[2]) // go

mySlice[0] = "you"
fmt.Println(mySlice) // [you love go]
```

# Variadic

Functions can take an arbitrary number of final arguments, this can be done by using `...` in the method signature. A variadic function receives the variadic argument as a **slice**.

```go
func concat(strs ...string) string {
	final := ""
	
	for i := 0; i < len(strs); i++ {
	        final += strs[i]
	    }
	    return final 
	}
```

## Spread Operator

The spread operator allows us to pass a slice _into_ a variadic function. The spread operator consists of three dots following the slice in the function call.

```go
func printStrings(strings ...string) {
	for i := 0; i < len(strings); i++ {
		fmt.Println(strings[i])
	}
}

func main() {
    names := []string{"bob", "sue", "alice"}
    printStrings(names...)
}
```

# Append

The built-in `append` function is used to dynamically add elements to a slice

If the underlying array is not large enough, append will create a new underlying array and point the returned slice to it.

**Code Examples:**

```go
slice = append(slice, oneThing)
slice = append(slice, firstThing, secondThing)
slice = append(slice, anotherSlice...)
```

# Range

Go provides syntactic sugar to iterate easily over elements of a slice:

```go
for INDEX, ELEMENT := range SLICE {
}
```

**Code Example:**

Instead of writing loop syntax like this:

```go
for i := 0; i < len(costs); i++ { 
	if costs[i].day == day { 
		dayCosts = append(dayCosts, costs[i].value) 
	} 
}
```

We can do using `range`

```go
for _,c := range costs {
		if c.day == day {
			dayCost = append(dayCost, c.value)
		}
	}
```

**Another Code Example:**

```go
func indexOfFirstBadWord(msg []string, badWords []string) int {
	for i, message := range msg {
		for _, badMessage := range badWords {
			if message == badMessage {
				return i
			}
		}
	}

	return -1
}
```

> [!tip]
> Remember, you can ignore returned values with an underscore `_` instead of creating an unused variable.

# Slices of Slices

Slices can hold other slices, creating a matrix, or a 2D slice.

```go
rows := [][]int{}
rows = append(rows, []int{1, 2, 3})
rows = append(rows, []int{4, 5, 6})
fmt.Println(rows)
// [[1 2 3] [4 5 6]]
```

**Code Example:**

```go
func createMatrix(rows, cols int) [][]int {
	matrix := [][]int{}
	for i := 0; i < rows; i++ {
		line := make([]int, 0)
		for j := 0; j < cols; j++ {
			line = append(line, i * j)
		}
		matrix = append(matrix, line)
	}
		return matrix
}

// Output
[0  0  0  0  0  0  0  0  0  0]
[0  1  2  3  4  5  6  7  8  9]
[0  2  4  6  8 10 12 14 16 18]
[0  3  6  9 12 15 18 21 24 27]
[0  4  8 12 16 20 24 28 32 36]
```

# Tricky Slices

The `append()` function changes the underlying array of its parameter AND returns a new slice. This means that using `append()` on anything other than itself is usually a BAD idea.

```go
// don't do this!
someSlice = append(otherSlice, element)
```

---

# Code Example - 1

```go
package main

type Message interface {
	Type() string
}

type TextMessage struct {
	Sender  string
	Content string
}

func (tm TextMessage) Type() string {
	return "text"
}

type MediaMessage struct {
	Sender    string
	MediaType string
	Content   string
}

func (mm MediaMessage) Type() string {
	return "media"
}

type LinkMessage struct {
	Sender  string
	URL     string
	Content string
}

func (lm LinkMessage) Type() string {
	return "link"
}

// Don't touch above this line

func filterMessages(messages []Message, filterType string) []Message {
	filteredMessages := make([]Message, 0)

	for _, m := range messages {
		if m.Type() == filterType {
			filteredMessages = append(filteredMessages, m)
		}
	}

		return filteredMessages
}

```

# Code Example - 2

```go
package main

import (
	"strings"
)

type sms struct {
	id      string
	content string
	tags    []string
}

func tagMessages(messages []sms, tagger func(sms) []string) []sms {
	for i, _ := range messages {
		messages[i].tags = tagger(messages[i])
	}

		return messages
}

func tagger(msg sms) []string {
	tags := []string{}
	
	if strings.Contains(strings.ToLower(msg.content), "urgent") {
		tags = append(tags, "Urgent")
	}

	if strings.Contains(strings.ToLower(msg.content), "sale") {
		tags = append(tags, "Promo")
	}

		return tags
}
```
