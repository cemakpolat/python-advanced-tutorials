---
title: Go's Core Concepts
parent: Foundation of Go
nav_order: 2
---

## Chapter 1: Go's Core Concepts

This chapter introduces the fundamental building blocks of the Go programming language. Mastering these concepts is essential for writing efficient Go programs. We will explore variables, data types, operators, control flow, functions, and pointers. Even though understanding these concepts is easy, it is crucial to master these basics. We will also list all the usable keywords in the go programming language.

**1.0 Go Keywords**

The following table summarizes the core keywords in Go. These keywords have special meanings and cannot be used as identifiers (variable names, function names, etc.):

| Keyword     | Description                                                              | Usage Example                                                                               |
| :---------- | :----------------------------------------------------------------------- | :------------------------------------------------------------------------------------------ |
| `break`     | Terminates the nearest enclosing loop (for, switch, or select) statement.  | `for i := 0; i < 10; i++ { if i > 5 { break } }`                                           |
| `case`      | Used in switch statements to define a case.                              | `switch grade { case "A": ... }`                                                            |
| `chan`      | Specifies that a variable is a channel.                                 | `c := make(chan int)`                                                                        |
| `const`     | Declares a constant value.                                             | `const pi = 3.14`                                                                           |
| `continue`  | Skips the rest of the current loop iteration.                          | `for i := 0; i < 10; i++ { if i%2 == 0 { continue } }`                                      |
| `default`   | Used in a switch statement to define a default case.                   | `switch grade { default: ... }`                                                              |
| `defer`     | Schedules a function call to be run after the surrounding function returns. | `defer file.Close()`                                                                          |
| `else`      | Used in if statements to specify code to execute if the condition is false. | `if x > 0 { ... } else { ... }`                                                             |
| `fallthrough`| Forces execution to continue to the next case in a switch statement.      | `switch i { case 1: ...; fallthrough; case 2: ... }`                                         |
| `for`       | Specifies a loop.                                                      | `for i := 0; i < 10; i++ { ... }`                                                           |
| `func`      | Defines a function.                                                     | `func add(x, y int) int { ... }`                                                            |
| `go`        | Starts a new goroutine.                                                | `go myFunction()`                                                                           |
| `goto`      | Transfers control to a labeled statement.                                 | `goto myLabel` (Use sparingly!)                                                                |
| `if`        | Specifies a conditional statement.                                       | `if x > 0 { ... }`                                                                           |
| `import`    | Imports packages.                                                        | `import "fmt"`                                                                              |
| `interface` | Defines an interface type.                                             | `type MyInterface interface { ... }`                                                         |
| `map`       | Specifies that a variable is a map.                                     | `myMap := make(map[string]int)`                                                               |
| `package`   | Declares the package name.                                               | `package main`                                                                                |
| `range`     | Iterates over elements in arrays, slices, strings, maps, and channels.   | `for index, value := range myArray { ... }`                                                   |
| `return`    | Exits a function and optionally specifies a return value.               | `return x + y`                                                                                |
| `select`    | Used in a select statement to choose from multiple channel operations.     | `select { case <-chan1: ...; case <-chan2: ... }`                                             |
| `struct`    | Defines a struct type.                                                  | `type Person struct { ... }`                                                                   |
| `switch`    | Specifies a multi-way branch based on the value of an expression.      | `switch value { case 1: ...; default: ... }`                                                 |
| `type`      | Declares a new type (alias or struct).                                   | `type MyInt int`                                                                              |
| `var`       | Declares a variable.                                                     | `var x int`                                                                                   |

**1.1 Variables and Data Types**

In Go, variables are used to store data. Every variable has a specific data type that determines the kind of value it can hold.

**1.1.1 Basic Data Types**

Go provides several built-in data types:

*   **`int`:** Represents integers (whole numbers).
    *   Examples: `10`, `-5`, `0`.
    *   Go offers different integer types based on size (e.g., `int8`, `int16`, `int32`, `int64`, `uint8`, `uint16`, `uint32`, `uint64`) based on your need.
*   **`float`:** Represents floating-point numbers (numbers with decimal points).
    *   Examples: `3.14`, `-2.5`, `0.0`.
    *   Go provides `float32` and `float64` for single and double-precision floating-point numbers, respectively.
*   **`bool`:** Represents boolean values (either `true` or `false`).
    *   Example: `true`, `false`.
*   **`string`:** Represents a sequence of characters.
    *   Examples: `"Hello, World!"`, `"Go is awesome!"`.

**1.1.2 Type Declarations and Inference**

There are two ways to declare variables in Go: explicit type declaration and type inference.

*   **Explicit Type Declaration:** You explicitly specify the data type of the variable:

    ```go
    var age int
    var price float64
    var name string
    var isValid bool
    ```
*   **Type Inference:** Go can infer the type of the variable based on the value assigned to it, using the short variable declaration syntax (`:=`):

    ```go
    age := 25         // age is inferred as int
    price := 99.99   // price is inferred as float64
    name := "Alice"    // name is inferred as string
    isValid := true    // isValid is inferred as bool
    ```
    You can also explicitly declare a type with the short variable declaration syntax. This is done like so:
    ```go
    age := int(25) // age is int
    ```

**1.1.3 Constants**

Constants are variables whose values cannot be changed after they are declared. You declare them using the `const` keyword:

```go
const pi float64 = 3.14159
const appName string = "My Application"
const maxAttempts int = 3
```

**1.2 Operators**

Operators are symbols that perform operations on one or more operands.

**1.2.1 Arithmetic Operators**

These perform basic mathematical calculations:

*   `+` (Addition): `a + b`
*   `-` (Subtraction): `a - b`
*   `*` (Multiplication): `a * b`
*   `/` (Division): `a / b`
*   `%` (Modulus): `a % b` (remainder after integer division)

**1.2.2 Relational Operators**

These compare two operands and return a boolean value (`true` or `false`):

*   `==` (Equal to): `a == b`
*   `!=` (Not equal to): `a != b`
*   `>` (Greater than): `a > b`
*   `<` (Less than): `a < b`
*   `>=` (Greater than or equal to): `a >= b`
*   `<=` (Less than or equal to): `a <= b`

**1.2.3 Logical Operators**

These combine boolean expressions:

*   `&&` (Logical AND): `a && b` (true if both `a` and `b` are true)
*   `||` (Logical OR): `a || b` (true if either `a` or `b` is true)
*   `!` (Logical NOT): `!a` (true if `a` is false)

**1.2.4 Bitwise Operators**

These operate on the binary representation of integers:

*   `&` (Bitwise AND): `a & b`
*   `|` (Bitwise OR): `a | b`
*   `^` (Bitwise XOR): `a ^ b`
*   `&^` (Bitwise AND NOT): `a &^ b`
*   `<<` (Left shift): `a << b`
*   `>>` (Right shift): `a >> b`

**1.2.5 Operator Precedence**

Operators have precedence (order in which they are evaluated). For example, multiplication and division have higher precedence than addition and subtraction. You can use parentheses to control the order of evaluation.

**1.3 Control Flow**

Control flow statements control the execution order of code.

**1.3.1 `if`, `else if`, `else` Statements**

The `if` statement executes a block of code if a condition is true. You can chain multiple conditions using `else if` and provide a default block with `else`:

```go
age := 20
if age >= 18 {
    fmt.Println("Adult")
} else if age >= 13 {
    fmt.Println("Teenager")
} else {
    fmt.Println("Child")
}
```

**1.3.2 `for` Loops**

The `for` loop is the only looping construct in Go. It has several forms:

*   **Basic `for` Loop:**

    ```go
    for i := 0; i < 5; i++ {
        fmt.Println(i)
    }
    ```
*   **`for` Loop with Condition:**

    ```go
    i := 0
    for i < 5 {
       fmt.Println(i)
       i++
    }
    ```
*   **`for` Range Loop:** Iterates over elements of arrays, slices, maps, and strings.

    ```go
    numbers := []int{1, 2, 3, 4, 5}
    for index, value := range numbers {
        fmt.Println(index, value)
    }
    ```
* **`for` Loop Without Condition**
    ```go
    i := 0
    for {
        if i > 5 {
            break
        }
        fmt.Println(i)
        i++
    }
    ```

**1.3.3 `switch` Statements**

The `switch` statement provides a more efficient way to handle multiple conditions:

```go
grade := "B"
switch grade {
case "A":
    fmt.Println("Excellent")
case "B":
    fmt.Println("Good")
case "C":
    fmt.Println("Average")
default:
    fmt.Println("Needs improvement")
}
```

**1.4 Functions**

Functions are blocks of code that perform a specific task.

**1.4.1 Function Declarations and Definitions**

```go
func add(a int, b int) int {
    return a + b
}
```

*   `func`: Keyword to declare a function.
*   `add`: Function name.
*   `(a int, b int)`: Parameter list (name and type).
*   `int`: Return type.

**1.4.2 Parameters and Return Values**

Functions can accept parameters as inputs and return values as outputs. In the previous example, the add function accept two inputs and returns a signal output.

**1.4.3 Multiple Return Values**

Functions can return multiple values, allowing to return results and errors:

```go
func divide(a float64, b float64) (float64, error) {
    if b == 0 {
        return 0, fmt.Errorf("division by zero")
    }
    return a / b, nil
}
```

**1.4.4 Variadic Functions**

Variadic functions can accept a variable number of arguments:

```go
func sum(numbers ...int) int {
    total := 0
    for _, num := range numbers {
        total += num
    }
    return total
}
```
In this example, we don’t know how many items we will receive as an input, and the expected item is a list of items.

**1.4.5 Anonymous Functions**

Functions can be defined without a name and assigned to a variable:

```go
add := func(a int, b int) int {
    return a + b
}
result := add(5, 3)
```
There is a similarity between parameter declaration and function definition when the variable is declared without specific keyword. In the above code, we assign a function to a parameter, which enable us to use it as a function. 

**1.5 Pointers**

Pointers store memory addresses of variables. This means, whenever we look at the address, we see the location of the variable, basically where it is stored in the memory. This is a powerful feature for a language, since it allow us to not move a variable, rather we keep its address and we pass this address to other functions or variables without doing any copy of it. 
Understanding pointers can be challenging for many developers, but the underlying logic is quite simple. However, improper usage can lead to serious issues. To make the concept easier to grasp, let’s use an analogy: a home address.
Imagine that you want to receive letters. Anyone who wants to send you mail needs your home address. In this analogy, your home is like a memory location, and your address is the pointer—it tells others where to find you.
Now, suppose you move to a new house. Your physical home (the actual memory) has changed, but if you don’t update your address (the pointer), people will keep sending mail to the wrong place, leading to lost messages—just like a dangling pointer in programming.
This analogy highlights why managing pointers correctly is crucial. Just as you must update your address when you move, in programming, you must ensure pointers reference valid memory locations to avoid unexpected behavior.

**1.5.1 Memory Addresses and Pointers**

```go
age := 30
ptr := &age // &age gets the address of age
```

**1.5.2 Dereferencing and Address-of Operators**

*   `&` (Address-of Operator): Gets the memory address of a variable.
*   `*` (Dereference Operator): Accesses the value stored at the memory address.
    ```go
    fmt.Println(ptr) // prints memory address
    fmt.Println(*ptr) // prints the value stored at the memory address
    *ptr = 31; // modifying the value stored in the memory address
    fmt.Println(*ptr)
    fmt.Println(age); // printing the variable age to prove that is modified through the pointer
    ```

**1.5.3 When to Use Pointers**

Pointers are useful in situations like:

*   Modifying the values of variables inside functions.
*   Passing large data structures to functions without copying.
*   Dynamic memory allocation.


**1.6 Key Takeaways**

This section summarizes the key concepts and practical skills covered in this chapter:

* **Go Keywords:**
    *   Go has several keywords, all with different purposes, such as control flow, variables declaration, functions, etc.
*   **Variables and Data Types:**
    *   Go supports basic data types such as `int`, `float`, `bool`, and `string`.
    *   Variables can be declared with explicit types or through type inference using `:=`.
    *   Constants are declared using `const` and cannot be modified after declaration.
*   **Operators:**
    *   Go includes arithmetic, relational, logical, and bitwise operators.
    *   Operator precedence determines the order in which operations are evaluated.
*   **Control Flow:**
    *   `if`, `else if`, `else` statements are used for conditional execution.
    *   `for` loops provide the primary mechanism for iteration.
    *   `switch` statements enable efficient handling of multiple conditions.
*   **Functions:**
    *   Functions are defined using the `func` keyword and can accept parameters and return values.
    *   Go functions can return multiple values.
    *   Variadic functions accept a variable number of arguments.
    *   Anonymous functions can be assigned to variables and used as callbacks.
*   **Pointers:**
    *   Pointers store memory addresses of variables.
    *   The `&` operator retrieves the memory address of a variable.
    *   The `*` operator dereferences a pointer to access the value stored at the memory address.
    *   Pointers are useful for modifying variables inside functions and avoiding unnecessary data copying.

This chapter has covered the fundamental building blocks for writing effective go code. With these core concepts, you are now equipped to start building basic go programs. We will use this knowledge as we explore more advanced topics in the next chapters.
