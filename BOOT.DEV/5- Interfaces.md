# Interfaces in Go

When a type implements an interface, it can then be used as that interface type.

```go
func printShapeData(s shape) {
	fmt.Printf("Area: %v - Perimeter: %v\n", s.area(), s.perimeter())
}
```

Because we say the input is of type `shape`, we know that any argument must implement the `.area()` and `.perimeter()` methods.

**Code Example:**

```go
package main

import (
	"fmt"
	"time"
)

func sendMessage(msg message) (string, int) {
	return msg.getMessage(), len(msg.getMessage()) * 3
}

type message interface {
	getMessage() string
}

type birthdayMessage struct {
	birthdayTime  time.Time
	recipientName string
}

func (bm birthdayMessage) getMessage() string {
	return fmt.Sprintf("Hi %s, it is your birthday on %s", bm.recipientName, bm.birthdayTime.Format(time.RFC3339))
}

type sendingReport struct {
	reportName    string
	numberOfSends int
}

func (sr sendingReport) getMessage() string {
	return fmt.Sprintf(`Your "%s" report is ready. You've sent %v messages.`, sr.reportName, sr.numberOfSends)
}
```

# Interface Implementation

Interfaces are implemented _implicitly_.
A type never declares that it implements a given interface. If an interface exists and a type has the proper methods defined, then the type automatically fulfills the interface.

```ad-info
A quick way of checking whether a struct implements an interface is to declare a function that takes an interface as an argument. If the function can take the struct as an argument, then the struct implements the interface.
```

# Interfaces Are Implemented Implicitly

A type implements an interface by implementing its methods. Unlike in many other languages, there is no explicit declaration of intent, there is no `implements` keyword.

> [!tldr]
> Remember, interfaces are collections of method signatures. A type "implements" an interface if it has all of the methods of the given interface defined on it.

# Name Your Interface Parameters

```go
type Copier interface {
  Copy(sourceFile string, destinationFile string) (bytesCopied int)
}
```

# Type Assertions in Go

An **interface** is a disguise. A `circle` can wear the `shape` disguise, but underneath it's still a `circle` with all its original properties.

A **type assertion** is just you pulling off that disguise to see what's underneath.

```go
type shape interface {
	area() float64
}

type circle struct {
	radius float64
}

func (c circle) area() float64 {
	// ...
}

func printShapeInfo(s shape) {
	c, ok := s.(circle)
	if ok {
		radius := c.radius
		fmt.Printf("s is a circle, radius: %v\n", radius)
		return
	}
}
```

```go
c, ok := s.(circle)
```

Read this as: _"Hey Go, try to unwrap `s` as a `circle`. If it works, give me the real circle in `c` and set `ok` to true."_

|Variable|What it holds|
|---|---|
|`c`|The unwrapped `circle` struct (only useful if `ok` is true)|
|`ok`|`true` if `s` really was a `circle`, `false` if not|

**Another Code Example:**

```go
func getExpenseReport(e expense) (string, float64) {
	em, ok := e.(email)
	if ok {
		return em.toAddress, em.cost()
	}

	sm, ok := e.(sms)
	if ok {
		return sm.toPhoneNumber, sm.cost()
	}

	return "", 0.0
}
```

