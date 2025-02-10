---
title: Appendix A
parent: Appendecies
nav_order: 1
---

## Appendix A: Go Tooling

Go has a robust set of built-in tools that can help you write, build, test, and manage your Go projects. Here’s an overview of some of the most important tools:

*   **`go build`:**
    *   **Purpose:** Compiles Go source files into executable binaries.
    *   **Usage:**
        ```bash
        go build <package_path>
        ```
        *   `<package_path>`:  Specifies the package or file to build. If omitted, it will build the package in the current directory.
		*   Use `./...` to build the entire project recursively.
	* You can also specify an output file for the build using the `-o` flag.
    *   **Example:**

        ```bash
        go build ./...
		go build -o output main.go
        ```

*   **`go run`:**
    *   **Purpose:** Compiles and runs Go source files directly without creating an executable.
    *   **Usage:**

        ```bash
        go run <go_file>
        ```
        *   `<go_file>`: Specifies the Go file to execute. You can also specify the path to the folder where the `main` function is located.
    *   **Example:**

        ```bash
        go run main.go
		go run ./...
        ```

*   **`go test`:**
    *   **Purpose:** Executes unit tests in Go packages.
    *   **Usage:**

        ```bash
        go test <package_path>
        ```
        *   `<package_path>`: Specifies the package or file containing tests to run.
		* Use `./...` to execute all tests recursively from the current directory.
		*  Use the `-run` flag to specify the test cases you want to run. For example, `go test -run TestAdd ./...` will only run the tests with the name `TestAdd`.
		*   Use the `-v` flag to get more verbose output.
    *   **Example:**

        ```bash
        go test ./...
        go test -v ./mypackage
		go test -run TestAdd ./...
        ```

*   **`go mod`:**
    *   **Purpose:** Manages Go modules, including initializing modules, adding dependencies, updating dependencies, and managing versions.
	*  `go mod init`: Initializes a new Go module.
	* `go mod tidy`: Updates the dependencies of the `go.mod` file, removing any unused ones, and adding new ones.
    *   **Common Usage:**

        ```bash
		go mod init <module_path>
		go mod tidy
        ```

*   **`go fmt`:**
    *   **Purpose:** Formats Go code according to Go's official coding style.
    *   **Usage:**

        ```bash
        go fmt <package_path>
        ```
		*   `<package_path>`: Specifies the package to format. Use `./...` to format all go files from the current directory.
    *   **Example:**

        ```bash
        go fmt ./...
        ```
		You can use `go fmt` with the `-w` flag to automatically write the changes to the files.

*  **`go vet`:**
    *   **Purpose**: Analyzes go code and reports on common mistakes.
    *   **Usage:**
        ```bash
		go vet ./...
        ```
		* `package_path` Specifies the package to check. Use `./...` to check the entire directory recursively.

*   **`go doc`:**
    *   **Purpose:** Displays documentation for Go packages and symbols.
    *   **Usage:**

        ```bash
        go doc <package>.<symbol>
        ```
		*  `<package>.<symbol>`: Specifies the package and symbol for which documentation is to be shown.
    *   **Example:**

        ```bash
        go doc fmt.Println
        ```

*  **`pprof`:**
	* **Purpose**: Pprof is a tool for profiling and visualizing Go programs, allowing to identify performance and memory bottlenecks.
	* You can use it to profile CPU usage using the flag `-cpuprofile` and memory allocations using the flag `-memprofile`.
	```bash
	go test -bench=. -cpuprofile cpu.prof
	```
	```bash
	go test -bench=. -memprofile mem.prof
	```
	You can then use the `go tool pprof` tool to view the results, by providing the profile file.
	```bash
	go tool pprof cpu.prof
	```
	```bash
	go tool pprof mem.prof
	```

These are just a few of the most important go tools, but you can see a full list of them by typing `go help` in your terminal.

