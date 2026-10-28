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

A `WaitGroup` lets your program wait until several goroutines have finished.

Without it, `main()` might finish and terminate the entire program before your goroutines complete their work.

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

## Example

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

func worker(id int) {
	fmt.Printf("Worker %d starting\n", id)

	// Simulate some work.
	time.Sleep(time.Second)

	fmt.Printf("Worker %d done\n", id)
}

func main() {
	var wg sync.WaitGroup

	for i := 1; i <= 5; i++ {
		wg.Go(func() {
			worker(i)
		})
	}

	// Wait here until all five workers finish.
	wg.Wait()

	fmt.Println("All workers finished")
}
```

Each call to `wg.Go(...)`:

1. Starts the supplied function as a goroutine.
2. Registers that goroutine with the `WaitGroup`.
3. Automatically marks it as finished when the function returns.

`wg.Wait()` pauses `main()` until every registered goroutine has finished. The workers can start and finish in a different order each time because they run concurrently

## A small real-world analogy

Imagine downloading three files concurrently:

```go
var wg sync.WaitGroup

for _, url := range urls {
	wg.Go(func() {
		download(url)
	})
}

wg.Wait()

fmt.Println("All downloads completed")
```

---

# Rate Limiting

Rate limiting controls how often your program performs an action.

 ## 1. Basic Rate Limiting

We start by placing five requests in a channel:

```go
requests := make(chan int, 5)

for i := 1; i <=5; i++ {
	request <- i
}

close(requests)
```

We can imagine them as a queue

```json
request 1 → request 2 → request 3 → request 4 → request 5
```

Next, we create a **ticker**

```go
limiter := time.Tick(200 * time.Millisecond)
```

This produces a value on a channel approximately every **200 milliseconds**. Each value is like a permission slip saying:

> “You may process one request now.”

The requests are then handled:

```go
for req := range requests {
	<-limiter
	fmt.Println("request", req, time.Now())
}
```

The most important line here is:

```go
<- limiter
```

it means:

> “Wait until the ticker gives me permission before continuing.”

The resulting timeline looks approximately like this:

```text
200 ms  → request 1
400 ms  → request 2
600 ms  → request 3
800 ms  → request 4
1000 ms → request 5
```

One request every 200 milliseconds equals:

```text
1000 ms ÷ 200 ms = 5 requests per second
```

Without `<-limiter`, all five requests could be processed almost immediately.

## 2. Allowing Short Bursts

Sometimes a strict gap between every request is too restrictive.

Imagine a user has been inactive for a while and then suddenly clicks three buttons. You might want to:

- Allow the first **3 requests immediately**.
- Continue processing additional requests at **5 per second**.

This is called **burstable rate limiting**.

We first create a buffered channel:

```go
burstyLimiter := make(chan time.Time, 3)
```

Think of this as a bucket that can hold **three permission tokens.**

Initially, it is filled:

```go
for range 3 {
	burstyLimiter <- time.Now()
}
```

A goroutine attempts to add another token every 200ms :

```go
go func() {
	for t := range time.Tick(200 * time.Millisecond) {
		burstyLimiter <- t
	}
}()
```

Requests consume those tokens:

```go
for req := range burstyRequests {
	<-burstyLimiter
	fmt.Println("request", req, time.Now())
}
```

```ad-tldr
Request 1 ─┐
Request 2 ─┼─ Immediately
Request 3 ─┘

Request 4 ─── About 200 ms later
Request 5 ─── About 200 ms after that
```

```ad-resources
For production applications, the package [https://pkg.go.dev/golang.org/x/time/rate](https://pkg.go.dev/golang.org/x/time/rate) provides a ready-made token-bucket limiter:

```go
limiter := rate.NewLimiter(rate.Limit(5), 3)
```
