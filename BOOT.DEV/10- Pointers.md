# Introduction to Pointers

**Syntax:**

```go
var p *int
```

The `&` operator generates a pointer to its operand

```go
myString := "Hello World"
myStringPtr := &myString
```

# References

```go
myString := "hello"      // myString is just a string
myStringPtr := &myString // myStringPtr is a pointer to myString's address

fmt.Printf("value of myStringPtr: %v\n", myStringPtr)
// value of myStringPtr: 0x140c050
```

## Dereference

The `*` operator dereferences a pointer to get the original value.

```go
*myStringPtr = "world" // set myString through the pointer
fmt.Printf("value of myString: %s\n", *myStringPtr) // read myString through the pointer
// value of myString: world
```

# Pass by Reference

```go
func increment(x *int) {
    *x++
    fmt.Println(*x)
    // 6
}

func main() {
    x := 5
    increment(&x)
    fmt.Println(x)
    // 6
}
```

## Fields of Pointers

When your function receives a pointer to a struct, you might try to access a field like this and encounter an error:

```go
msgTotal := *analytics.MessagesTotal
```

Instead, access it – like you'd normally do — using a [selector expression](https://go.dev/ref/spec#Selectors).

```go
msgTotal := analytics.MessagesTotal
```

This approach is the recommended, simplest way to access struct fields in Go, and is shorthand for:

```go
(*analytics).MessagesTotal
```

**Code Example:**

```go
package main

type Analytics struct {
	MessagesTotal     int
	MessagesFailed    int
	MessagesSucceeded int
}

type Message struct {
	Recipient string
	Success   bool
}

func analyzeMessage(an *Analytics, msg Message) {
	if msg.Success {
		(*an).MessagesTotal++
		(*an).MessagesSucceeded++
	} else {
		(*an).MessagesTotal++
		(*an).MessagesFailed++
	}	
}
```

# Nil Pointers

If a pointer points to nothing (`nil`), then dereferencing it will cause a `panic` during runtime.

# Pointer Receivers

A receiver type on a method can be a pointer. Methods with pointer receivers can modify the value to which the receiver points. 

Methods with pointer receivers don't require that a pointer is used to call the method. The pointer will automatically be derived from the value.

**Code Example:**

```go
type car struct {
	color string
}

func (c *car) setColor(color string) {
	c.color = color
}

func main() {
	c := car{
		color: "white",
	}
	c.setColor("blue")
	fmt.Println(c.color)
	// prints "blue"
}
```

**Code Example:**

```go
type circle struct {
	x int
	y int
    radius int
}

func (c *circle) grow() {
    c.radius *= 2
}

func main() {
    c := circle{
        x: 1,
        y: 2,
        radius: 4,
    }

    // notice c is not a pointer in the calling function
    // but the method still gains access to a pointer to c
    c.grow()
    fmt.Println(c.radius)
    // prints 8
}
```

# Code Example

 *Why `*customer` (Pointer) Instead of `customer` (Value)?*
	`updateBalance` needs to modify the original customer's balance.

```go
package main

import (
	"errors"
)

type customer struct {
	id      int
	balance float64
}

type transactionType string

const (
	transactionDeposit    transactionType = "deposit"
	transactionWithdrawal transactionType = "withdrawal"
)

type transaction struct {
	customerID      int
	amount          float64
	transactionType transactionType
}

func updateBalance(cust *customer, trans transaction) error {
	if (trans.transactionType != transactionDeposit) && (trans.transactionType != transactionWithdrawal) {
		return errors.New("unknown transaction type")
	}

	if trans.transactionType == transactionWithdrawal {
		if cust.balance < trans.amount {
			return errors.New("insufficient funds")
		}

		cust.balance -= trans.amount
	} else if trans.transactionType == transactionDeposit {
		cust.balance += trans.amount
	}

	return nil
}

```

