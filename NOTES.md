# NOTES

## Code organization

* Go code is organized using this hierarchy:
    * **modules**: I think a module is collection of folders and files that are contained within a folder, so each module has to have one, and only one, `go.mod` file. The `go.mod` file is important because it defines the module path, which is the import path prefix for packages in the module. So when you want to import code from other modules in your project, you have to reference the correct module path.
        * **packages**: A package is a way to group functions, and it's made up of all the files in the same directory.
            * **code files**: These are the code files that contain your functions, etc.
* Naming convention for module paths (which is the module's name): `prefix/descriptive-text`
    * The `prefix` should be something unique like a Github account name or a company or an author or owner name.
    * For the `descriptive-text`, a good choice would be a project name. Remember that package names carry most of the weight of describing functionality. The module path creates a namespace for those package names.
* Before you can run your Go code, you need to create a `go.mod` file with this command: `go mode init <prefix>/<module-name>`
* 

## How to compile and execute code

* Once a `go.mod` file has been created inside your project root directory, you can run the `main` function with this command: `go run .`. 
* What should the name of the file be that contains the `main()` function? I don't think it matters. But what does matter is that the `main()` that is inside of the `main` package will be executed by default when you run the main package (with `go run .`).

## How to work with modules and imports

You can import modules like the `rsc.io/quote` module like this:

```go
package main

import "fmt"

import "rsc.io/quote"

func main() {
    fmt.Println(quote.Go())
}
```

Then add new module requirements (i.e. install the module inside your project) and sums with this command: `go mod tidy`
