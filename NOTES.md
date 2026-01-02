# NOTES

## Code organization

* Go code is organized using this hierarchy: modules >> packages >> code files. Go code is grouped into packages, and packages are grouped into modules. Your module specifies dependencies needed to run your code, including the Go version and the set of other modules it requires.
    * **modules**: A Go project is called a module. A module is not just source code. It is also an exact specification of the dependencies of the code within the module. Every module has a `go.mod` file in its root directory. 
        * Your project can be setup as a module, which has a number of advantages — including making it much easier to manage third-party dependencies, avoid supply-chain attacks, and ensure reproducible builds of your application in the future. I think a module is a collection of folders and files that are contained within a folder, so each module has to have one, and only one, `go.mod` file. The `go.mod` file is important because it defines the module path, which is the import path prefix for packages in the module. So when you want to import code from other modules in your project, you have to reference the correct module path.
    * **packages**: A package is a way to group functions, and it's made up of all the files in the same directory.
            * The `main` package in a Go module contains the code that starts a Go program. All Go programs start from the `main()` function in the `main` package.
    * **code files**: These are the code files that contain your functions, etc.
* Naming convention for module paths (which is the module's name): `prefix/descriptive-text`
    * The `prefix` should be something unique like a domain name, a Github account name, a company name, or an author or owner name.
    * For the `descriptive-text`, a good choice would be a project name. Remember that package names carry most of the weight of describing functionality. The module path creates a namespace for those package names.


## Dependency tracking and how to add dependencies

`go mod` is the Go dependency manager and your dependencies are tracked inside your `go.mod` files. Before you can run your Go code, you need to create a `go.mod` file with this command: `go mod init <module-name>` (e.g. `go mod init my.domain.com`, `go mod init example.com/project_name`)

You shouldn't edit the `go.mod` file directly. Instead, use the `go get` and `go mod tidy` commands to manage changes to the file.

You can use the `go get` command to add dependencies to your `go.mod` file. For example, in the command line navigate to the module where you want to add the dependecy (the folder with the `go.mod` file) and enter this command:

```
go get golang.org/x/example/hello/reverse
```

This will add the dependcy to your module's `go.mod` file.


## How to import modules

### Published modules

You can import modules like the `rsc.io/quote` module like this:

```go
package main

import "fmt"

import "rsc.io/quote"

func main() {
  fmt.Println(quote.Go())
}
```

Then run `go mod tidy` to add new module requirements (i.e. install the module inside your project) and sums.

The `go mod tidy` command will add package imports to your `go.mod` file and create a `go.sum` file that appears to have checksum values in it. The `go.mod` file acts like a package lock file that preserves package versions.


### Local (non-published) modules

For more details see the tutorial at [Call your code from another module](https://go.dev/doc/tutorial/call-module-code).

```go
package main

import (
  "fmt"

  // Import local module
  "example.com/greetings"
)

func main() {
  // Get a greeting message and print it.
  message := greetings.Hello("Gladys")
  fmt.Println(message)
}
```

**Step 1: Add a `replace` directive that points to the local module**

In a terminal window, navigate to the module directory that is importing the local module and run the `go mod edit` command:

Syntax: `go mod edit -replace <module-path-prefix>/<module-path-descriptive-text>=../<module-directory-name>`

Example: `go mod edit -replace example.com/greetings=../greetings`

The command specifies that `example.com/greetings` should be replaced with `../greetings` for the purpose of locating the dependency. 

After you run the `go mod edit` command, the `go.mod` file should include a `replace` directive.

**Step 2: Synchronize the module dependencies**

In a terminal window, navigate to the module directory that is importing the local module and run `go mod tidy`.

This will add a `require` directive to the `go.mod` file.


## Unit tests

See this page for details: https://go.dev/doc/tutorial/add-a-test


## How to compile and execute code

* Once a `go.mod` file has been created inside your project root directory, you can run the `main()` function with this command: `go run path/to/main.go` (or the name of the file where your `main()` function is located).
* What should the name of the file be that contains the `main()` function? I don't think it matters. But what does matter is that the `main()` function that is inside of the `main` package will be executed by default when you run the main package (with `go run <filename>.go`).
* Code executed as an application must be in a `main` package.


## Automating with Makefiles

You can/should create build scripts with Makefiles that are used while developing software in Go. This will help to automate things like formatting and debugging before compiling your code.

Here is an example of a Makefile that you can use with Go development:

```makefile
# The .DEFAULT_GOAL defines which target is run when no target is specified. In this case, the default is the build target. So when you run `make` (without specifying a target), you will see output like this:
# $ make
# go clean ./...
# go fmt ./...
# go vet ./...
# go build
.DEFAULT_GOAL := build

# The .PHONY line keeps make from getting confused if a directory or file in your project has the same name as one of the listed targets.
.PHONY: clean fmt vet build

# Each possible operation is called a target and the following definitions are the target definitions. 
# The word before the colon (:) is the name of the target. 
# Any words after the target (like `vet` in the line `build: vet`) are the other targets that must be run before the specified target runs. 
# The tasks that are performed by the target are on the indented lines after the target.
# Using ./... tells a Go tool to apply the command to all the files in the current directory and all subdirectories.
clean:
	go clean ./...

fmt: clean
	go fmt ./...

vet: fmt
	go vet ./...

build: vet
	go build
```
