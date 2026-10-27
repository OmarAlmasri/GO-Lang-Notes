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

## Multiple Goroutines

The order of execution of goroutine is random, so we might not get the output as expected.

```ad-info
We can also start a goroutine for an anonynous function call
**Example:**
```

```go
go func(msg string) {
        fmt.Println(msg)
    }("going")
```


---

# Go Channel

Channels in Go act as a medium for goroutines to communicate with each other.

We know that goroutines are used to create concurrent programs. Concurrent programs can run multiple processes.

However, sometimes there might be a situation where two or more goroutines need to communicate with one another. In such situations, we use channels that allow goroutines to communicate and share resources with each other.

## Create Channel in Go

In Go, we use the `make()` function to create a channel. For example,

```go
channelName := make(chan int)
```

- `channelName` - name of the channel
- `(chan int)` - indicates that the channel is of integer type

### Example: Go Channel

```go
package main
import "fmt"

func main() {

	// create channel of integer type
	number := make(chan int)
	
	// access type and value of channel
	fmt.Printf("Channel Type: %T\n", number)
	fmt.Printf("Channel Value: %v", number)
}
```

**Output**

```txt
Channel Type: chan int
Channel Value: 0xc00007a060
```

## Golang Channel Operations

Once we create a channel, we can send and receive data between different goroutines through the channel.

**1. Send data to the channel**

The syntax to send data to the channel is

```go
channelName <- data
```

Here the data after the `<-` operator is sent to `channelName`.

**Example:**

```go
// send integer data to channel
number <- 15
message <- "Learning Go Channel"
```

**2. Receive data from the channel**

The syntax to receive data from the channel is

```go
<- channelName
```

This accesses the data from `channelName`

**Example:**

```go
// receive data 15
<- number

// receive data "Learning Go Channel"
<- message
```

## Example: Go Channel Operations

```go
package main
import "fmt"

func main() {

	// create channel
	number := make(chan int)
	message := make(chan string)
	
	// function call with goroutine
	go channelData(number, message)
	
	// retrieve channel data
	fmt.Println("Channel Data:", <-number)
	fmt.Println("Channel Data:", <-message)
}

func channelData(number chan int, message chan string) {
	
	// send data into channel
	number <- 15
	message <- "Learning Go Channel"
}
```

## Blocking Nature of Channel

In Go, the channel automatically blocks the send and receive operations depending on the status of goroutines.

**1. When a goroutine sends data into a channel, the operation is blocked until the data is received by another goroutine. For example,**

```go
package main
import "fmt"

func main() {

  // create channel
  ch := make(chan string)
 
  // function call with goroutine
  go sendData(ch)

  // receive channel data
  fmt.Println(<-ch)

}

func sendData(ch chan string) {
 
  // data sent to the channel
   ch <- "Received. Send Operation Successful"
   fmt.Println("Message sent! Send Operation Complete")

}
```

**Output:**

```txt
Message sent! Send Operation Complete
Received. Send Operation Successful
```

**2.** **When a goroutine receives data from a channel, the operation is blocked until another goroutine sends the data to the channel. For example,**

```go
package main
import "fmt"

func main() {

  // create channel
  ch := make(chan string)

  // function call with goroutine
  go receiveData(ch)

  fmt.Println("No data. Receive Operation Blocked")

  // send data to the channel 
  ch <- "Data Received. Receive Operation Successful" 

}

func receiveData(ch chan string) {

  // receive data from the channel
  fmt.Println(<-ch)

}
```

**Output:**

```txt
No data. Receive Operation Blocked
Data Received. Receive Operation Successful
```

---

# Go Select

The select statement in Go allows us to execute a channel among many alternatives.

**Syntax of Select Statement**

```go
select {

	case firstChannel:
	
	case secondChannel:
	
	case thirdChannel:
}
```

Here, each case of the select represents an individual channel. And, based on the availability of channel operations, the select statement executes a channel.

## Example: Golang select Statement

```go
package main
import "fmt"

func main() {

	// Create two channels
	number := make(chan int)
	message := make(chan string)
	

	// function call with goroutine
	go channelNumber(number)
	go channelMessage(message)

	// select and execute a channel
	select {
		case firstChannel := <- number:
			fmt.Println("Channel Data", firstChannel)
		
		case secondChannel := <- message:
			fmt.Println("Channel Data", secondChannel)
	}
}

func channelNumber(number chan int) {
	number <- 15
}

func channelMessage(message chan string) {
	message <- "Learning Go Select"
}
```

## Go select with one Channel Ready for Execution

We know that when both multiple channels are ready for execution, the select statement executes the channel randomly.

However, if only one of the channels is ready for execution, it executes that channel. For example,

```go
package main
import (
  "fmt"
  "time"
)

func main() {

  // create channels
  number := make(chan int)
  message := make(chan string)

  // function call with goroutine
  go channelNumber(number)
  go channelMessage(message)

  // selects and executes a channel
  select {
    case firstChannel := <-number:
      fmt.Println("Channel Data:", firstChannel)

    case secondChannel := <-message:
      fmt.Println("Channel Data:", secondChannel)
  }

}

// goroutine to send data to the channel
func channelNumber(number chan int) {
  number <- 15
}

// goroutine to send data to the channel
func channelMessage(message chan string) {
  
  // sleeps the process by 2 seconds
  time.Sleep(2 * time.Second)

  message <- "Learning Go Select"
}
```

## Go select to block Channels

The select statement blocks all channels if they are not ready for execution. Suppose, in our previous example, if both the number and message channels are not ready for execution, select blocks both the channels for a certain time until one is available for execution.

## Golang select with the default case

When none of the channels are ready, the select blocks the program. However, it's better to display some messages instead of blocking until the channels are ready.

For this, we use the `default` case, which is executed if none of the channels are ready for execution. For example,

```go
package main

import (
  "fmt"
  "time"
)

func main() {

  // create channels
  number := make(chan int)
  message := make(chan string)

  // function call with goroutine
  go channelNumber(number)
  go channelMessage(message)

  // selects and executes a channel
  select {
    case firstChannel := <-number:
      fmt.Println("Channel Data:", firstChannel)

    case secondChannel := <-message:
      fmt.Println("Channel Data:", secondChannel)
	
    // default case 
    default:
      fmt.Println("Wait!! Channels are not ready for execution")
  }

}

// goroutine to send data to the channel
func channelNumber(number chan int) {
  
  // sleeps the process for 2 seconds
  time.Sleep(2 * time.Second)

  number <- 15
}

// goroutine to send data to the channel
func channelMessage(message chan string) {

  // sleeps the process by 2 seconds
  time.Sleep(2 * time.Second)
 
  message <- "Learning Go Select"
}
```

**Output**

```txt
Wait!! Channels are not ready for execution
```

---

# WaitGroup

Let's see a simple code snippet illustrating the problem we might face when using goroutines

```go
package main

import "fmt"

func runner1() {
    fmt.Print("\nI am first runner")
}

func runner2() {
    fmt.Print("\nI am second runner")
}

func execute() {
    go runner1()
    go runner2()

}

func main() {
  
    // Launching both the runners
    execute()
}
```

When we run this code, we won't get any output, this because as soon as we launch both the goroutines, our main function just got terminated. And every program in Golang executes until main function is not terminated.

**What can we do with this problem?**

1. We can wait for some time after launching the runners. For this purpose, we can use the `Sleep` function from `time` package.

```go
package main

import (
    "fmt"
    "time"
)

func runner1() {
    fmt.Print("\nI am first runner")
}

func runner2() {
    fmt.Print("\nI am second runner")
}

func execute() {
    go runner1()
    go runner2()

}

func main() {

    // Launching both the runners
    execute()
    time.Sleep(time.Second)
}
```

The problem is that, our executors executes in bit amount of time so we unnecessarily  blocking the program for 1 sec.

2. Let's use another Golang's standard library primitive `sync.WaitGroup`. `WaitGroup` is actually a type of counter which blocks the execution of function (or might say A goroutine) until its internal counter become 0.

### How It Works ?

`WaitGroup` exports 3 methods.

| 1   | **Add(int)** | It increases WaitGroup counter by given integer value.                                      |
| --- | ------------ | ------------------------------------------------------------------------------------------- |
| 2   | **Done()**   | It decreases WaitGroup counter by 1, we will use it to indicate termination of a goroutine. |
| 3   | **Wait()**   | It Blocks the execution until it's internal counter becomes 0.                              |

```ad-note
WaitGroup is concurrency safe, so its safe to pass pointer to it as argument for Groutines.
```
```go
package main

import (
    "fmt"
    "sync"
)

func runner1(wg *sync.WaitGroup) {
    defer wg.Done() // This decreases counter by 1
    fmt.Print("\nI am first runner")

}

func runner2(wg *sync.WaitGroup) {
    defer wg.Done()
    fmt.Print("\nI am second runner")
}

func execute() {
    wg := new(sync.WaitGroup)
    wg.Add(2)

    // We are increasing the counter by 2
    // because we have 2 goroutines
    go runner1(wg)
    go runner2(wg)

    // This Blocks the execution
    // until its counter become 0
    wg.Wait()
}

func main() {
    // Launching both the runners
    execute()
}
```

The output is same as the `Sleep` program but we out program doesn't block for 1 sec .