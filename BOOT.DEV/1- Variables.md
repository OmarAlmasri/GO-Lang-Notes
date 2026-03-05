
```Go title="Declaring Variables"
var mySkillIssues int
mySkillIssues = 42
```

**Example:**

```Go title="Vars example"
    var smsSendingLimit int
    var costPerSMS float64
    var hasPermission bool
    var username string

    fmt.Printf("%v %.2f %v %q\n", smsSendingLimit, costPerSMS, hasPermission, username)
```

We can declare vars using **Walrus Operator**

```Go title="Walrus Operator"
mySkillIssues := 42
pi := 3.14159
message := "Hello, world!"
isGoat := true
```


# Same Line Declarations

You can declare multiple variables on the same line:

```go
mileage, company := 80276, "Toyota"
```

# Constants

Declared with `const`, can't use `:=` with them.

```go
const pi = 3.14159
```

# Formatting Strings in Go

## Default Representation

The `%v` variant prints any value in a default format. It can be used as a catchall.

```go
s := fmt.Sprintf("I am %v years old", 10)
// I am 10 years old

s := fmt.Sprintf("I am %v years old", "way too many")
// I am way too many years old
```

If you want to print in a more specific way, you can use the following formatting verbs:

## String

```go
s := fmt.Sprintf("I am %s years old", "way too many")
// I am way too many years old
```
## Integer

```go
s := fmt.Sprintf("I am %d years old", 10)
// I am 10 years old
```

## Float

```go
s := fmt.Sprintf("I am %f years old", 10.523)
// I am 10.523000 years old

// The ".2" rounds the number to 2 decimal places
s := fmt.Sprintf("I am %.2f years old", 10.523)
// I am 10.52 years old
```

If you're interested in all the formatting options, you can look at the `fmt` package's [docs](https://pkg.go.dev/fmt#hdr-Printing).

# Runes and String Encoding

1. When you need to work with individual characters in a string, you should use the `rune` type. It breaks strings up into their individual characters, which can be more than one byte long.
2. We can include a wide variety of Unicode characters in our strings, such as emojis and Chinese characters, and Go will handle them just fine.

