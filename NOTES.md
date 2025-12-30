# NOTES

## Code organization

* Go code is organized using this hierarchy: modules >> packages >> code files. Go code is grouped into packages, and packages are grouped into modules. Your module specifies dependencies needed to run your code, including the Go version and the set of other modules it requires.
    * **modules**: I think a module is a collection of folders and files that are contained within a folder, so each module has to have one, and only one, `go.mod` file. The `go.mod` file is important because it defines the module path, which is the import path prefix for packages in the module. So when you want to import code from other modules in your project, you have to reference the correct module path.
    * **packages**: A package is a way to group functions, and it's made up of all the files in the same directory.
    * **code files**: These are the code files that contain your functions, etc.
* Naming convention for module paths (which is the module's name): `prefix/descriptive-text`
    * The `prefix` should be something unique like a domain name, a Github account name, a company name, or an author or owner name.
    * For the `descriptive-text`, a good choice would be a project name. Remember that package names carry most of the weight of describing functionality. The module path creates a namespace for those package names.
* Before you can run your Go code, you need to create a `go.mod` file with this command: `go mode init <prefix>/<module-name>`
* 

## How to compile and execute code

* Once a `go.mod` file has been created inside your project root directory, you can run the `main` function with this command: `go run .`. 
* What should the name of the file be that contains the `main()` function? I don't think it matters. But what does matter is that the `main()` that is inside of the `main` package will be executed by default when you run the main package (with `go run .`).
* Code executed as an application must be in a `main` package.

## How to work with modules and imports

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

After you run the `go mode edit` command, the `go.mod` file should include a `replace` directive.

**Step 2: Synchronize the module dependencies**

In a terminal window, navigate to the module directory that is importing the local module and run `go mod tidy`.

This will add a `require` directive to the `go.mod` file.
