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

Go programs are organized into _packages_. A package is a directory of Go code that's all compiled together. Functions, types, variables, and constants defined in one source file are visible to **all other source files within the same package (directory)**.

A _repository_ contains one or more _modules_. A module is a collection of Go packages that are released together.

![[modules.png]]

A file named `go.mod` at the root of a project declares the module. It contains:

- The module path
- The version of the Go language your project requires
- Optionally, any external package dependencies your project has

The module path is just the import path prefix for all packages within the module. Here's an example of a `go.mod` file:

```go
module github.com/bootdotdev/exampleproject

go 1.26.0

require github.com/google/examplepackage v1.3.0
```

Each module's path not only serves as an import path prefix for the packages within but _also indicates where the go command should look to download it_. For example, to download the module `golang.org/x/tools`, the go command would consult the repository located at [https://golang.org/x/tools](https://golang.org/x/tools).


```ad-example
An "import path" is a string used to import a package. A package's import path is its module path joined with its subdirectory within the module. For example, the module `github.com/google/go-cmp` contains a package in the directory `cmp/`. That package's import path is `github.com/google/go-cmp/cmp`. Packages in the standard library do not have a module path prefix.
```

# Go Environment

## Directory Structure

To recap how packages and modules work in your project directory structure:

- You will have many _git repositories_ on your machine (typically one per project).
- Each repository is typically a single _module_.
- Each module contains one or more _packages_
- Each package consists of one or more _Go source files_ in a single directory.

# Remote Packages

We can download remote packages using `go get`

```go
go get github.com/wagslane/go-tinytime
```

