# Loops in Go

The basic loop in Go is written in standard C-like syntax:

```go
for INITIAL; CONDITION; AFTER{
  // do something
}
```

`INITIAL` is run once at the beginning of the loop and can create  
variables within the scope of the loop.

`CONDITION` is checked before each iteration. If the condition doesn't pass  
then the loop breaks.

`AFTER` is run after each iteration.

For example:

```go
for i := 0; i < 10; i++ {
  fmt.Println(i)
}
```

# Omitting Conditions from a for Loop in Go

The condition part can be removed from a loop in go, it can continue forever.

```go
for INITIAL; ; AFTER {
  // do something forever
}
```

# There Is No While Loop in Go

No need for `while` loop in Go, because we can use `for` and remove the parts we don't want to use from it's declaration (We can only keep the condition part).

```go
for CONDITION {
  // do some stuff while CONDITION is true
}
```

# Continue & Break

Just like other languages

