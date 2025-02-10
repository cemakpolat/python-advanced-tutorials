---
title: Testing and Benchmarking
parent: Advanced Topics
nav_order: 1
---

## Chapter 13: Testing and Benchmarking

Writing tests and measuring performance are essential parts of building robust and efficient software. Go provides built-in support for both, making it easier to ensure the quality and performance of your code. This chapter will guide you through the process of writing unit tests and performing benchmarks using Go.

**13.1 Writing Unit Tests with Go's `testing` Package**

Go's `testing` package provides a simple and powerful way to write unit tests. Tests are functions that verify the behavior of your code and must follow certain conventions.

*   **Test File Naming:** Test files must end with `_test.go` (e.g., `my_function_test.go`).
*   **Test Function Signatures:** Test functions must start with the word `Test` and have the following signature: `func TestMyFunction(t *testing.T) { ... }`.
*   **Using the `testing.T` Type:** The `testing.T` type provides methods for reporting errors and failures during testing:
    *   `t.Error(...)`: Reports an error and continues the test.
    *   `t.Errorf(...)`: Reports an error with formatted output and continues the test.
    *   `t.Fatal(...)`: Reports an error and stops the test immediately.
    *   `t.Fatalf(...)`: Reports an error with formatted output and stops the test immediately.
    *   `t.Run(name string, f func(t *testing.T))`: Creates a subtest which is useful to group and test different scenarios.
	*   `t.Skip(...)`: Skips the current test
*   **Example Test:**
	```go
	// my_function_test.go
    package mypackage
    
    import "testing"

	func add(a int, b int) int {
		return a + b;
	}

	func TestAdd(t *testing.T) {
		t.Run("Test with positive numbers", func(t *testing.T) {
		    result := add(5,3);
		    if result != 8 {
				t.Errorf("add(5,3) failed, expected 8, got %d", result)
		    }
		});
		
		t.Run("Test with negative numbers", func(t *testing.T) {
			result := add(-5,-3);
			if result != -8 {
				t.Errorf("add(-5,-3) failed, expected -8, got %d", result);
			}
		})
		
		t.Run("Test with mixed numbers", func(t *testing.T) {
			result := add(-5, 3)
			if result != -2 {
				t.Errorf("add(-5,3) failed, expected -2, got %d", result)
			}
		})
	}
	```
    *   The example above shows how to test a simple add function, with different scenarios, showing how the use of subtests can help in structuring your test cases.
	*   The test function `TestAdd` uses the testing methods to assert different aspects of the function.

*   **Running Tests:**
    To run tests, navigate to the directory containing your code and use the following command:

    ```bash
    go test ./...
    ```

	This will run all tests in the current directory and all subdirectories.
	
	You can also specify a test to be executed using the `-run` flag:
    ```bash
	go test -run TestAdd ./...
    ```
	This command will only execute tests with the name `TestAdd`.
*  **Skipping a test**: Sometimes you might need to skip a test if it is not relevant or if it needs some external condition to be met before executing the test. In that case, you can use the `t.Skip()` function
	```go
	// my_function_test.go
    package mypackage
    
    import (
		"testing"
		"os"
	)

	func add(a int, b int) int {
		return a + b;
	}

	func TestAdd(t *testing.T) {
		t.Run("Test with positive numbers", func(t *testing.T) {
		    result := add(5,3);
		    if result != 8 {
				t.Errorf("add(5,3) failed, expected 8, got %d", result)
		    }
		});

		if os.Getenv("ENABLE_SKIP_TEST") != "true" {
			t.Skip("skipping this test due to ENABLE_SKIP_TEST variable");
		}
		
		t.Run("Test with negative numbers", func(t *testing.T) {
			result := add(-5,-3);
			if result != -8 {
				t.Errorf("add(-5,-3) failed, expected -8, got %d", result);
			}
		})
		
		t.Run("Test with mixed numbers", func(t *testing.T) {
			result := add(-5, 3)
			if result != -2 {
				t.Errorf("add(-5,3) failed, expected -2, got %d", result)
			}
		})
	}
	```
	In the above example, if the environment variable `ENABLE_SKIP_TEST` is not set to `"true"` the `Test with negative numbers` subtest will be skipped.

**13.2 Using Benchmarking to Measure Performance**

Go's `testing` package also supports benchmarking to measure the performance of functions or code blocks. Benchmarks help you identify performance bottlenecks and compare different approaches.

*   **Benchmark File Naming:** Benchmark files must also end with `_test.go` (e.g., `my_function_test.go`).
*   **Benchmark Function Signatures:** Benchmark functions must start with the word `Benchmark` and have the following signature: `func BenchmarkMyFunction(b *testing.B) { ... }`.
*   **Using the `testing.B` Type:** The `testing.B` type provides methods for performing benchmarks:
	* `b.N`: represents the number of iterations the benchmark function will be called.
    *   `b.ResetTimer()`: Resets the timer before code that must be benchmarked.
    *   `b.StopTimer()`: Stops the timer to execute setup functions that do not need to be benchmarked.
    *   `b.StartTimer()`: Starts the timer to execute code that must be benchmarked.
*   **Example Benchmark:**
	```go
	// my_function_test.go
    package mypackage
    
    import "testing"

	func add(a int, b int) int {
		return a + b;
	}

    func BenchmarkAdd(b *testing.B) {
	   	for i := 0; i < b.N; i++ {
			add(i, i);
		}
    }
	```
    *   The example above shows how to benchmark the `add` function.
	*   The benchmark will execute the function for `b.N` times.
    *   It's important to use a for loop `for i := 0; i < b.N; i++` as a good practice to make sure the benchmark results are accurate.

*   **Running Benchmarks:**
    To run benchmarks, use the following command:

    ```bash
    go test -bench=. ./...
    ```

	This command will run all the benchmarks defined in the current folder and all its subfolders.
	
	You can also run benchmarks based on their name using the `-bench` flag.
    ```bash
	go test -bench=BenchmarkAdd ./...
    ```
	This command will execute the benchmarks that contains the string `BenchmarkAdd`.
	
	You can add the flag `-benchmem` to have more information about memory allocation:
	```bash
	go test -bench=. -benchmem ./...
	```

**13.3 Key Takeaways**

This chapter has explored how to write effective tests and benchmarks in Go:

*   **Unit Testing:** Use the `testing` package, writing test functions, and using `testing.T` to check the correctness of your code.
*   **Benchmarking:** Use the `testing` package, writing benchmark functions, and using `testing.B` to measure the performance of specific code blocks.
*   **Test and Benchmark conventions**: Make sure to adhere to all the testing and benchmarking conventions.
*  **Subtests**: Group your test cases into subtests, to provide a more comprehensive test.
*	**Skipping**: Learn how to skip tests, in situations where you might want to run certain tests under certain conditions.

By applying the concepts learned in this chapter, you will be able to build higher quality software while improving your code and make sure that it is as efficient as possible.

