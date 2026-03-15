# The Error Interface

Go programs express errors with `error` values. An Error is any type that implements the simple built-in [error interface](https://blog.golang.org/error-handling-and-go):

```go
type error interface {
    Error() string
}
```

When something go wrong in a function, it should return `error` value as its last return value. Any code that calls a function that can return an `error` value should handle error by checking if the error is `nil`.

```go title="Example Implementation"
func Atoi(s string) (int, error)
```

```go title="Using Atoi Safely"
i, err := strconv.Atoi("42b")
if err != nil {
    fmt.Println("couldn't convert:", err)
    return
}
```

# Formatting Strings Review

**Default Values:**

using `%v`

```go
const name = "Kim"
const age = 22
s := fmt.Sprintf("%v is %v years old.", name, age)
// s = "Kim is 22 years old."
```

**Rounding Floats:**

using `%f`

```go
s := fmt.Sprintf("I am %f years old", 10.523)
// s = I am 10.523000 years old

// The ".2" rounds the number to 2 decimal places
s := fmt.Sprintf("I am %.2f years old", 10.523)
// s = I am 10.52 years old
```

# The Error Interface

We can build our own types of error by implementing the `error` interface.

**Code Example:**

```go
type userError struct {
    name string
}

func (e userError) Error() string {
    return fmt.Sprintf("%v has a problem with their account", e.name)
}

func sendSMS(msg, userName string) error {
    if !canSendToUser(userName) {
        return userError{name: userName}
    }
    ...
}
```

**Another Code Example:**

```go
package main

import (
	"fmt"
)

type divideError struct {
	dividend float64
}

func (d divideError) Error() string{
	return fmt.Sprintf("can not divide %v by zero", d.dividend)
}

func divide(dividend, divisor float64) (float64, error) {
	if divisor == 0 {
		return 0, divideError{dividend: dividend}
	}
	return dividend / divisor, nil
}

```

# The Errors Package

The Go standard library provides an "errors" package that makes it easy to deal with errors.

Read the godoc for the [errors.New()](https://pkg.go.dev/errors#New) function, but here's a simple example:

```go
var err error = errors.New("something went wrong")
```

**Dividing by Zero Example Simplified:**

```go
package main

import (
	"errors"
)

func divide(x, y float64) (float64, error) {
	if y == 0 {
		return 0.0, errors.New("no dividing by 0")
	}
	return x / y, nil
}
```

# Panic

Don't use this. It crashes the app and throws a stack trace.

**Code Example:**

```go
func enrichUser(userID string) User {
    user, err := getUser(userID)
    if err != nil {
        panic(err)
    }
    return user
}

func main() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("recovered from panic:", r)
        }
    }()

    // this panics, but the defer/recover block catches it
    // a truly astonishingly bad way to handle errors
    enrichUser("123")
}
```

