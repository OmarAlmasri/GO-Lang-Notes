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

