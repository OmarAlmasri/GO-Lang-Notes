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

# Type Switches

A type switch is similar to a regular switch statement, but the cases specify _types_ instead of _values_.

```go
unc printNumericValue(num interface{}) {
	switch v := num.(type) {
	case int:
		fmt.Printf("%T\n", v)
	case string:
		fmt.Printf("%T\n", v)
	default:
		fmt.Printf("%T\n", v)
	}
}
```

```go title="Code Example"
func getExpenseReport(e expense) (string, float64) {
	switch e := e.(type) {
	case email:
		return e.toAddress, e.cost()
	case sms:
		return e.toPhoneNumber, e.cost()
	}
	return "", 0.0
}
```

# Code Example

```go title="Example 1"
package main

type formatter interface {
	format() string
}

type plainText struct {
	message string
}


func (p plainText) format() string {
	return p.message
}

type bold struct {
	message string
}

func (b bold) format() string {
	return "**" + b.message + "**"
}

type code struct {
	message string
}


func (c code) format() string {
	return "`" + c.message + "`"
}

func sendMessage(format formatter) string {
	return format.format()
}
```


```go title="Example 2"
package main

type notification interface {
	importance() int
}

type directMessage struct {
	senderUsername string
	messageContent string
	priorityLevel  int
	isUrgent       bool
}

func (dm directMessage) importance() int {
	if dm.isUrgent {
		return 50
	}

	return dm.priorityLevel
}

type groupMessage struct {
	groupName      string
	messageContent string
	priorityLevel  int
}

func (gm groupMessage) importance() int {
	return gm.priorityLevel
}

type systemAlert struct {
	alertCode      string
	messageContent string
}

func (sa systemAlert) importance() int {
	return 100
}

func processNotification(n notification) (string, int) {
	switch m := n.(type) {
		case directMessage:
			return m.senderUsername, m.importance()
		case groupMessage:
			return m.groupName, m.importance()
		case systemAlert:
			return m.alertCode, m.importance()
	}
		return "",0
}

```

