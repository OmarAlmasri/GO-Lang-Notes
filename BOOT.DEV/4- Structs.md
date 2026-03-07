# Structs in Go

```go
type car struct {
	brand      string
	model      string
	doors      int
	mileage    int
}
```

# Nested Structs in Go

Structs can be nested to represent more complex entities:

```go
type car struct {
  brand string
  model string
  doors int
  mileage int
  frontWheel wheel
  backWheel wheel
}

type wheel struct {
  radius int
  material string
}
```

The fields of a struct can be accessed using the dot `.` operator.

```go
myCar := car{}
myCar.frontWheel.radius = 5
```

# Anonymous Structs in Go

An anonymous struct is just like a normal struct, but it is **defined without a name** and therefore cannot be referenced elsewhere in the code.

To create an anonymous struct, just instantiate the instance immediately using a second pair of brackets after declaring the type:

```go
myCar := struct {
  brand string
  model string
} {
  brand: "Toyota",
  model: "Camry",
}
```

You can even nest anonymous structs as fields within other structs:

```go
type car struct {
  brand string
  model string
  doors int
  mileage int
  // wheel is a field containing an anonymous struct
  wheel struct {
    radius int
    material string
  }
}

var myCar = car{
  brand:   "Rezvani",
  model:   "Vengeance",
  doors:   4,
  mileage: 35000,
  wheel: struct {
    radius   int
    material string
  }{
    radius:   35,
    material: "alloy",
  },
}
```

**When should we use Anonymous Structs?**

In general, always use named structs because they're reusable. Use anonymous structs when you know that you'll never use this structs again.

# Embedded Structs

Go isn't **Object Oriented**. It doesn't support Classes or Inheritance. But embedded structs are a way to elevate and **share fields between struct definitions.**

```go
type car struct {
  brand string
  model string
}

type truck struct {
  // "car" is embedded, so the definition of a
  // "truck" now also additionally contains all
  // of the fields of the car struct
  car
  bedSize int
}
```

## Embedded vs. Nested

- Unlike nested structs, an embedded struct's fields are accessed at the top level like normal fields.
- Like nested structs, you assign the promoted fields with the embedded struct in a [composite literal](https://golang.org/ref/spec#Composite_literals).

```go
lanesTruck := truck{
  bedSize: 10,
  car: car{
    brand: "Toyota",
    model: "Tundra",
  },
}

fmt.Println(lanesTruck.brand) // Toyota
fmt.Println(lanesTruck.model) // Tundra
```

# Struct Methods in Go

Go supports methods that can be defined on structs.  Methods are just functions that have a receiver. A **receiver** is a special parameter that syntactically goes _before_ the name of the function.

```go
type rect struct {
  width int
  height int
}

// area has a receiver of (r rect)
// rect is the struct
// r is the placeholder
func (r rect) area() int {
  return r.width * r.height
}

var r = rect{
  width: 5,
  height: 10,
}

fmt.Println(r.area())
// prints 50
```

Receivers are important because they will allow us to define interfaces that our structs (and other types) can implement.

```go title="Struct Methods Example"
package main

type authenticationInfo struct {
	username string
	password string
}

func (a authenticationInfo) getBasicAuth() string {
	return "Authorization: Basic " + a.username + ":" + a.password
}
```

# Memory Layout

In short, align the fields by size from largest to smallest.

# Empty Struct

[Empty structs](https://dave.cheney.net/2014/03/25/the-empty-struct) are used in Go as a [unary](https://en.wikipedia.org/wiki/Unary_operation) value.

```go

// anonymous empty struct type
empty := struct{}{}

// named empty struct type
type emptyStruct struct{}
empty := emptyStruct{}
```

They use **zero memory**

# Code Example #1

```go
package main

type User struct {
	Membership
	Name string
}

type Membership struct {
	Type string
	MessageCharLimit int
}

func newUser(name string, membershipType string) User {
	if membershipType == "premium" {
		return User {
			Membership: Membership{
				Type: membershipType,
				MessageCharLimit: 1000,
			},
			Name: name,
		}
	}

	return User {
		Membership: Membership{
			Type: membershipType,
			MessageCharLimit: 100,
		},
		Name: name,
	}

}
```

# Code Example #2

```go
package main

func (u User) SendMessage(message string, messageLength int) (string, bool) {	
	if (messageLength <= u.MessageCharLimit) {
		return message, true
	}

	return "", false
}

type User struct {
	Name string
	Membership
}

type Membership struct {
	Type             string
	MessageCharLimit int
}

func newUser(name string, membershipType string) User {
	membership := Membership{Type: membershipType}
	if membershipType == "premium" {
		membership.MessageCharLimit = 1000
	} else {
		membership.Type = "standard"
		membership.MessageCharLimit = 100
	}
	return User{Name: name, Membership: membership}
}

```

