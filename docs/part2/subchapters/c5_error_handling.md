---
title: Error Handling
parent: Advanced Go Development
nav_order: 3
---


## Chapter 5: Error Handling

In Go, error handling is an essential part of writing robust and reliable code. Go encourages explicit error handling, which means you must check for errors after operations that can fail. This chapter explains the mechanisms and best practices for handling errors gracefully.

**5.1 The `error` Type**

The `error` type in Go is an interface that represents an error condition. It has a single method:

```go
type error interface {
	Error() string
}
```

Any type that implements the `Error() string` method can be used as an `error`. The most common way to create errors is by using the `errors` package.

*   **Creating Simple Errors:**
    ```go
    package main

    import (
        "fmt"
	    "errors"
    )

    func main() {
        err := errors.New("an error occurred")
        if err != nil {
            fmt.Println("Error:", err)
        }
    }
    ```

*   **Returning Errors from Functions:**
    Functions that can fail often return an `error` as their last return value.
    ```go
     package main

    import (
        "fmt"
	    "errors"
    )
    
    func divide(a, b float64) (float64, error){
        if b == 0 {
            return 0, error.New("division by zero")
        }
        return a / b, nil // returnin nil as no error has occured
    }

    func main() {
        result, err := divide(10, 2)
        if err != nil {
            fmt.Println("Error:", err)
        } else {
            fmt.Println("Result:", result)
        }

	    result, err = divide(10,0)
        if err != nil {
            fmt.Println("Error:", err)
        } else {
            fmt.Println("Result:", result)
        }
    }
    ```
    In this example, the `divide` function returns an error when the divisor is zero. The caller of the `divide` function is then responsible for handling that error.

**5.2 Error Handling Best Practices**

Go promotes a straightforward approach to error handling: check errors where they occur and handle them appropriately.

*   **Explicit Error Checking:** Check the error returned by functions. Do not ignore errors, as this can lead to unexpected behavior and bugs.

    ```go
	package main

    import (
        "fmt"
        "os"
        "io"
        )

    func main() {
        file, err := os.Open("myfile.txt")
	    if err != nil {
		    fmt.Println("Error opening file: ", err);
		    return; // Returning early to prevent execution of the rest of the function
	    }
	    defer file.Close()
	
	    contents, err := io.ReadAll(file)
	    if err != nil {
		    fmt.Println("Error reading file: ", err);
		    return;
	    }
	
	    fmt.Println(string(contents))
    }
    ```
    In this example, the code checks for error after calling both `os.Open` and `io.ReadAll`, using early returns to prevent further execution if an error occured.

*   **Descriptive Errors:** Use errors that provide meaningful messages to help diagnose and fix issues.

    ```go
	package main

    import (
        "fmt"
        "errors"
        )
   
    func validateAge(age int) error {
	    if age < 0 {
		    return fmt.Errorf("invalid age: age cannot be negative %d", age);
	    }

	    if age > 120 {
		    return fmt.Errorf("invalid age: age cannot exceed %d, got %d", 120, age);
	    }

	    return nil; // all good
    }

    func main() {
	    err := validateAge(-5);
	    if err != nil {
		fmt.Println("Error:", err);
	    }

	    err = validateAge(150);
	    if err != nil {
		    fmt.Println("Error:", err);
	    }

	    err = validateAge(50);
	    if err != nil {
		    fmt.Println("Error: ", err); // will not enter this code block
	    } else {
		    fmt.Println("Valid age") // This will be executed instead
	    }
    }
    ```
    In the above example the error messages are clear and more detailed by including relevant information such as the age value that caused the error.
*   **Error Wrapping:** When you receive an error from another function, you can "wrap" it by adding additional context. This is useful for tracing the origin of the error.
     ```go
	package main

    import (
        "fmt"
        "errors"
        )
    func doSomething() error {
	    return errors.New("an internal error happened")
    }

    func outerFunction() error {
        err := doSomething()
        if err != nil {
            // Using fmt.Errorf() for error wrapping with context
            return fmt.Errorf("error in outerFunction: %w", err);
        }

        return nil;
        }

    func main() {
	    err := outerFunction();
	    if err != nil {
    		fmt.Println("Error: ", err);
	    }
    }
    ```
   By using `%w` as a formatting verb for `fmt.Errorf`, you'll create a wrapped error, preserving the original error and giving more context about where it originated.
*   **Handling Errors Gracefully:** Decide how to handle the error: log it, return it, retry, or terminate the program.
    ```go
	package main

    import (
        "fmt"
        "errors"
        "net/http"
        )
    func fetchDataFromURL(url string) ([]byte, error) {
	    res, err := http.Get(url)
	    if err != nil {
		    return nil, fmt.Errorf("Error fetching data from the url: %w", err)
	    }
	    defer res.Body.Close()
	    
	    if res.StatusCode != http.StatusOK {
		return nil, fmt.Errorf("error with status code: %d", res.StatusCode);
	    }

	    contents := make([]byte, 0)

	    _, err = res.Body.Read(contents)
	    if err != nil {
		    return nil, fmt.Errorf("error reading contents: %w", err);
	    }

	    return contents, nil;
    }

    func main() {
	    url := "https://someinvalidurl.com"
	    data, err := fetchDataFromURL(url);

	    if err != nil {
		    fmt.Println("Error fetching data:", err)
		    // Handle the error in a graceful manner - log it, return it, retry
	    } else {
		    fmt.Println(data);
	    }
    }
    ```
    In this example, there is a decision to catch the error when fetching a url, log it and gracefully continue (or potentially return to allow the caller to handle it better), instead of simply crashing the application.

**5.3 Panics and Recovering**

Panics and Recover are features to handle exceptional situations which shouldn't happen in the normal execution of the program.

*   **Panics:** A panic is a signal that an unexpected error occurred during program execution. When a panic happens, the program usually stops. Panics should be used in very exceptional situations, mainly those which should never occur, as they will bring down the entire application.
   ```go
	package main

    import (
        "fmt"
    )

    func someFunction(a int) {
		if a == 0 {
			panic("division by zero")
		}
		fmt.Println("Result: ", 100/a);
    }

    func main() {
	    someFunction(5)
	    someFunction(0); // This will trigger the panic
	    someFunction(2) // This will not execute
    }
    ```
    In the above example, we see that a panic has happened when `a` is 0, which caused the entire execution of the program to halt.
*   **Recovering from Panics:** The `recover` function is used to regain control from a panicking goroutine. It can only be used in a deferred function.
    !TODO: ADD HERE MORE EXPLANATION!
   ```go
	package main

    import (
        "fmt"
    )

    func someFunction(a int) {
        defer func() {
            if r := recover(); r != nil {
                fmt.Println("recovered from panic:", r);
            }
        }()
	
        if a == 0 {
            panic("division by zero")
        }
		fmt.Println("Result: ", 100/a);
    }

    func main() {
        someFunction(5)
        someFunction(0) // This will trigger the panic but the program wont stop
	    someFunction(2) // this will execute normally
    }
    ```

   In the above example, we are using a `defer` statement combined with the `recover()` function. This allows us to catch a panic in a safe manner, allowing us to gracefully handle situations that would otherwise stop the execution of our program.

**Important:** Avoid using `panic` and `recover` for normal error handling. They are intended for very exceptional cases. Use them sparingly and only when necessary.

**5.4 Key Takeaways**

This chapter has covered the fundamental aspects of error handling in Go:

*   **The `error` Type:** Errors are represented by the built-in `error` interface, requiring the implementation of the `Error() string` method.
*   **Error Handling Best Practices:**
    *   Explicitly check for errors returned by functions.
    *   Use descriptive and informative error messages.
    *   Wrap errors to provide context about their origin.
    *   Handle errors gracefully based on the context and criticality.
*   **Panics and Recovering:**
    *   Panics signal an exceptional error that usually halts program execution.
    *   The `recover` function, used within a deferred function, is used to regain control from a panicking goroutine.
    *   Use panics only for very exceptional situations that should never happen in the normal execution of the application.

By adhering to these principles, you can build Go applications that are robust and less prone to unexpected crashes and errors.

