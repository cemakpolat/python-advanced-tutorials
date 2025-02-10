---
title: Data Structures in Go
parent: Foundation of Go
nav_order: 3
---
## Chapter 2 : Data Structures in Go

This chapter explores fundamental data structures provided by Go, which allow you to organize and manipulate data effectively. Understanding these structures is crucial for building robust and efficient applications.

**2.1 Arrays and Slices**

Arrays and slices are used to store collections of elements of the same type.

**2.1.1 Arrays vs. Slices**

*   **Arrays:** Arrays have a **fixed** size, which is determined at the time of declaration. Once created, the size of an array cannot be changed.
*   **Slices:** Slices are **dynamic** in size and are more commonly used than arrays in Go. Slices are a view into an underlying array. They provide a more flexible and convenient way to work with sequences of data.

**2.1.2 Creating and Initializing Arrays and Slices**

*   **Arrays:**

    ```go
    package main

    import "fmt"

    func main() {
        // Declare an array of 5 integers
        var numbers [5]int
        numbers[0] = 10
        numbers[1] = 20
        fmt.Println(numbers) // Output: [10 20 0 0 0]

        // Initialize an array with values
        grades := [3]int{90, 85, 95}
	    fmt.Println(grades) // Output: [90 85 95]
    }
    ```
*   **Slices:**

    ```go
    package main

    import "fmt"

    func main() {
        // Create a slice with a literal
        names := []string{"Alice", "Bob", "Charlie"}
	    fmt.Println(names) // Output: [Alice Bob Charlie]
        
        // Create a slice using make() function
        ages := make([]int, 5) // Creates a slice of length 5 with all elements initialized to 0
        fmt.Println(ages) // Output: [0 0 0 0 0]
        
        // Create a slice with initial length and capacity
        scores := make([]int, 3, 5) // Creates a slice of length 3 and capacity 5
        fmt.Println(scores) // Output: [0 0 0]
    }
    ```

**2.1.3 Slicing Operations and Appending Elements**

Slices can be sliced to create new slices that reference a portion of the underlying array:

```go
package main

import "fmt"

func main() {
    numbers := []int{1, 2, 3, 4, 5, 6}
    
    subSlice := numbers[1:4] // Creates a new slice from index 1 up to (but not including) 4
    fmt.Println(subSlice) // Output: [2 3 4]

    anotherSubSlice := numbers[:3] // From the start up to (but not including) index 3
    fmt.Println(anotherSubSlice) // Output: [1 2 3]

    yetAnotherSubSlice := numbers[3:] // From index 3 to the end
    fmt.Println(yetAnotherSubSlice) // Output: [4 5 6]
    
    // Appending elements to a slice
    numbers = append(numbers, 7, 8)
    fmt.Println(numbers) // Output: [1 2 3 4 5 6 7 8]
}
```

**2.1.4 Capacity and Length**

*   **Length:** The length of a slice is the number of elements it contains.
*   **Capacity:** The capacity is the number of elements in the underlying array starting from the first element in the slice. The capacity is the maximum length the slice can expand to before allocating a new underlying array.

```go
package main

import "fmt"

func main() {
    numbers := make([]int, 3, 5) // length = 3, capacity = 5
    fmt.Println(len(numbers))  // Output: 3
    fmt.Println(cap(numbers)) // Output: 5

    numbers = append(numbers, 10, 20) // length=5
    fmt.Println(len(numbers)) // Output: 5
    fmt.Println(cap(numbers)) // Output: 5
    
    numbers = append(numbers,30) //length = 6, cap will be expanded if necessary, in this case will be expanded to 10
    fmt.Println(len(numbers)) // Output 6
    fmt.Println(cap(numbers)) // Output: 10

}
```

**2.1.5 Working with Multi-Dimensional Slices**

Go supports multi-dimensional slices, which are slices of slices.

```go
package main

import "fmt"

func main() {
    matrix := [][]int{
        {1, 2, 3},
        {4, 5, 6},
        {7, 8, 9},
    }
    fmt.Println(matrix) // Output: [[1 2 3] [4 5 6] [7 8 9]]
}
```

**2.2 Maps (Dictionaries)**

Maps are unordered collections of key-value pairs. Keys must be of a comparable type, such as string, number, or pointer. Values can be of any type.

**2.2.1 Creating and Initializing Maps**

```go
package main

import "fmt"

func main() {
    // Create an empty map
    ages := make(map[string]int)
    ages["Alice"] = 30
    ages["Bob"] = 25
    fmt.Println(ages)  // Output: map[Alice:30 Bob:25]

    // Initialize map with literal values
    grades := map[string]float64{
        "Math":    92.5,
        "Science": 88.0,
	    "English": 75.3,
    }
    fmt.Println(grades) // Output: map[English:75.3 Math:92.5 Science:88]
}
```

**2.2.2 Adding, Accessing, and Deleting Map Elements**

```go
package main

import "fmt"

func main() {
    ages := map[string]int{
        "Alice": 30,
        "Bob":   25,
    }

    // Add an element
    ages["Charlie"] = 28

    // Access an element
    fmt.Println(ages["Alice"]) // Output: 30

    // Delete an element
    delete(ages, "Bob")
    fmt.Println(ages) // Output: map[Alice:30 Charlie:28]
}
```

**2.2.3 Checking for Element Existence**

You can check if a key exists in a map using the "comma ok" idiom:

```go
package main

import "fmt"

func main() {
    ages := map[string]int{
        "Alice": 30,
        "Bob":   25,
    }

    age, exists := ages["Alice"]
    if exists {
        fmt.Println("Age:", age) // Output: Age: 30
    } else {
        fmt.Println("Key not found")
    }

    age, exists = ages["Charlie"]
    if exists {
        fmt.Println("Age:", age)
    } else {
        fmt.Println("Key not found")  // Output: Key not found
    }
}
```

**2.3 Structs**

Structs are composite data types that group together related fields.

**2.3.1 Defining Structs and Creating Instances**

```go
package main

import "fmt"

type Person struct {
    Name string
    Age  int
}

func main() {
    // Create an instance of the Person struct
    person1 := Person{
        Name: "Alice",
        Age:  30,
    }
    fmt.Println(person1) // Output: {Alice 30}

    person2 := Person{"Bob", 25}
    fmt.Println(person2) // Output: {Bob 25}
}
```

**2.3.2 Accessing Struct Fields**

```go
package main

import "fmt"

type Person struct {
    Name string
    Age  int
}

func main() {
    person := Person{
        Name: "Alice",
        Age:  30,
    }
    fmt.Println("Name:", person.Name) // Output: Name: Alice
    fmt.Println("Age:", person.Age)  // Output: Age: 30
}
```

**2.3.3 Nested Structs**

Structs can contain other structs as fields:

```go
package main

import "fmt"

type Address struct {
    Street string
    City string
    Country string
}
type Employee struct {
    Name string
    Address Address
}

func main() {
    employee := Employee {
        Name: "Alice",
        Address: Address{
            Street: "124 Maing St",
            City: "Anytown",
            Country: "USA"
        }
    }
    fmt.Println("Employee Name:", employee.Name)
    fmt.Println("City:", employee.Adress.City)
}
```

**2.3.4 Struct Embedding**

Go allows embedding one struct within another, which provides a form of composition:

```go
package main

import "fmt"

type Name struct {
    FirstName string
    LastName  string
}

type Person struct {
    Name
    Age int
}

func main() {
    p := Person{
	    Name: Name{
		FirstName: "John",
		LastName: "Doe",
	    },
        Age:  30,
    }
	fmt.Println(p.FirstName) // Output: John
	fmt.Println(p.LastName) // Output: Doe
}
```

**2.4 Working with Strings**

Strings in Go are immutable sequences of bytes representing text.

**2.4.1 Basic String Manipulation**

```go
package main

import (
    "fmt"
    "strings"
)

func main() {
    str := "Hello, Go!"
    fmt.Println("Original:", str)

    // Get length of string
    fmt.Println("Length:", len(str))

    // Substring operation
    sub := str[0:5]
    fmt.Println("Substring:", sub)

    // Concatenating strings
    concat := str + " Welcome!"
    fmt.Println("Concatenated:", concat)
}
```

**2.4.2 Common String Functions**

Go’s `strings` package provides many useful functions for working with strings:

```go
package main

import (
    "fmt"
    "strings"
)

func main() {
    str := "  Go is Amazing!   "
    fmt.Println("Original:", str)

    // Trim leading and trailing whitespace
    trimmed := strings.TrimSpace(str)
    fmt.Println("Trimmed:", trimmed)

    // Convert to uppercase
    upper := strings.ToUpper(trimmed)
    fmt.Println("Uppercase:", upper)

    // Split into words
    words := strings.Split(trimmed, " ")
    fmt.Println("Split words:", words)
    
    // check if contains
    contains := strings.Contains(trimmed, "Amazing")
    fmt.Println("Contains Amazing?:", contains)
}
```

**2.4.3 Strings and Byte Slices**

Strings can be converted to byte slices and vice versa.

```go
package main

import "fmt"

func main() {
    str := "Hello"
    bytes := []byte(str)
    fmt.Println("Bytes:", bytes) // Output: Bytes: [72 101 108 108 111]
    newStr := string(bytes)
    fmt.Println("New string:", newStr)  // Output: New string: Hello
}
```

**2.5 Key Takeaways**

This chapter has covered several fundamental data structures in Go:

*   **Arrays and Slices:**
    *   Arrays have a fixed size, while slices are dynamic and more flexible.
    *   Slices are views into underlying arrays.
    *   Slicing and appending are common slice operations.
    *   Slices have a length and a capacity.
*   **Maps:**
    *   Maps store key-value pairs.
    *   Keys must be comparable types.
    *   Maps are created using `make` or with literal values.
    *   The "comma ok" idiom is used to check for element existence.
*   **Structs:**
    *   Structs group related data fields.
    *   Struct fields can be accessed using the dot operator.
    *   Structs can be nested.
    *   Struct embedding provides a form of composition.
*   **Strings:**
    *   Strings are immutable sequences of bytes.
    *   Common string operations like substring, concatenation, and trimming.
    *   Strings can be converted to byte slices.

By understanding these data structures, you are now better equipped to structure and manage data in Go applications. 
