# Packages

- Every Golang program is made up  of packages.
- The `main` package has an entry point which in the `main()` function.
- A package can also be a **library package**, a library package doesn't have an entry point. Libraries simply export functionality that can be used by other packages.

**Code Example:**

```go
package main

import (
	"fmt"
	"math/rand"
)

func main() {
	fmt.Println("My favorite number is", rand.Intn(10))
}
```

# Package Naming

By _convention_, a package's name is the same as the last element of its import path. For instance, the `math/rand` package comprises files that begin with:

```go
package rand
```

## One Package / Directory

A directory of Go code can have **at most** one package. All `.go` files in a single directory must all belong to the same package. If they don't, an error will be thrown by the compiler. This is true for main and library packages alike.

*Note:* You can name the package however you want without any issues but this is just the best practice.

# Modules

