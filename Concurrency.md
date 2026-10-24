# Goroutines

In Go, we use `Goroutines` to create concurrent programs.

## Create Goroutine

We can convert a regular function to a goroutine by calling the function using the `go` keyword.

```go
func display() {
  // code inside the function
}

// start goroutine
go display()
```

Here, `display()` is a goroutine.

---
## Run two functions concurrently using Goroutine

In a concurrent program, the `main()` is always a default goroutine. Other goroutines don't execute if the `main()` is not executing.

So, in order to make sure that all the goroutines are executed before the main function ends, we sleep the process so that the other processes get a chance to execute.

```go
package main
import (
	"fmt"
	"time"
)

func display(message string) {

	for {
		fmt.Println(message)
		time.Sleep(time.Second * 1)
	}
}

func main() {
	go display("Process 1")
	
	display("Process 2")
}
```

---

## Multiple Goroutines

The order of execution of goroutine is random, so we might not get the output as expected.

---

# Go Channel

Channels in Go act as a medium for goroutines to communicate with each other.

We know that goroutines are used to create concurrent programs. Concurrent programs can run multiple processes.

However, sometimes there might be a situation where two or more goroutines need to communicate with one another. In such situations, we use channels that allow goroutines to communicate and share resources with each other.

---

## Create Channel in Go