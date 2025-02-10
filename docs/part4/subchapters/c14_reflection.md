---
title: Reflection
parent: Advanced Topics
nav_order: 2
---

## Chapter 14: Reflection

Reflection is a powerful feature that allows a program to inspect and manipulate its own structure at runtime. In Go, reflection is provided through the `reflect` package. While it's a powerful tool, it should be used carefully because it can lead to less readable and maintainable code, and has a performance cost. This chapter will explain the concepts of reflection in Go and showcase some use cases.

**14.1 Understanding Reflection in Go**

Reflection is the ability of a program to examine its own structure and behavior at runtime. It allows programs to:

*   **Inspect Types:** Determine the type of a variable, including structs, functions, and interfaces.
*   **Access Fields:** Access fields of a struct by name.
*   **Call Functions:** Invoke functions dynamically based on their name or type.
*   **Modify Values:** Set values of variables.

Go's reflection is primarily provided by the `reflect` package. The central types in the `reflect` package are `Type` and `Value`:

*   **`reflect.Type`:** Represents the type of a Go value. You can obtain a `reflect.Type` using `reflect.TypeOf(variable)`.
*   **`reflect.Value`:** Represents the value of a Go variable. You can obtain a `reflect.Value` using `reflect.ValueOf(variable)`.

*   **Example of Using `reflect.TypeOf` and `reflect.ValueOf`:**
    ```go
    package main

    import (
        "fmt"
        "reflect"
    )

	type Person struct {
		Name string
		Age int
	}

    func main() {
        name := "John Doe"
		age := 30
		person := Person{Name:"Alice", Age:25}

        typeOfName := reflect.TypeOf(name)
		valueOfName := reflect.ValueOf(name);

		typeOfAge := reflect.TypeOf(age);
		valueOfAge := reflect.ValueOf(age);

		typeOfPerson := reflect.TypeOf(person);
		valueOfPerson := reflect.ValueOf(person);

        fmt.Println("Type of name:", typeOfName)   // Type of name: string
		fmt.Println("Value of name:", valueOfName); // Value of name: John Doe
		fmt.Println("Kind of name:", typeOfName.Kind()); // Kind of name: string

		fmt.Println("Type of age:", typeOfAge)  // Type of age: int
		fmt.Println("Value of age:", valueOfAge); // Value of age: 30
		fmt.Println("Kind of age:", typeOfAge.Kind()); // Kind of age: int
	
		fmt.Println("Type of person:", typeOfPerson); // Type of person: main.Person
		fmt.Println("Value of person:", valueOfPerson); // Value of person: {Alice 25}
		fmt.Println("Kind of person:", typeOfPerson.Kind()) // Kind of person: struct
    }
    ```
    *   The `reflect.TypeOf` returns a `reflect.Type` which represents the type of the variable passed as argument.
    *  The `reflect.ValueOf` returns a `reflect.Value` which represents the value of the variable.
    * The `.Kind()` method gives the basic kind of the variable (string, int, struct...).

*  **Accessing struct fields**
   ```go
	package main

    import (
		"fmt"
        "reflect"
	)

	type Person struct {
		Name string
		Age int
		Address Address
	}

	type Address struct {
		Street string
		City string
	}

    func main() {
		person := Person{
			Name: "Alice",
			Age: 25,
			Address: Address {
				Street: "Main Street 123",
				City: "New York",
			},
		}

		valueOfPerson := reflect.ValueOf(person);
		typeOfPerson := reflect.TypeOf(person);

		for i := 0; i < valueOfPerson.NumField(); i++ {
			field := typeOfPerson.Field(i);
			value := valueOfPerson.Field(i);
			fmt.Println("Field:", field.Name, "Type:", field.Type, "Value:", value)
			if field.Type.Kind() == reflect.Struct {
				for j := 0; j < value.NumField(); j++ {
					nestedField := value.Type().Field(j)
					nestedValue := value.Field(j)
					fmt.Println("\tNested Field:", nestedField.Name, "Nested Type:", nestedField.Type, "Nested Value:", nestedValue)
				}
			}
		}
    }
   ```
   *    The `NumField()` method returns the number of fields in a struct.
   *    The `Field(i)` method returns the `i`th field of the struct type.
	*   The example above shows how to iterate through all the fields of the `Person` struct and how to access its values using reflection. It also shows how to access nested fields in a struct.

*  **Setting struct fields**
    ```go
	package main

    import (
		"fmt"
        "reflect"
	)

	type Person struct {
		Name string
		Age int
	}

    func main() {
		person := Person{
			Name: "Alice",
			Age: 25,
		}

		valueOfPerson := reflect.ValueOf(&person).Elem(); // need to pass as pointer to allow setting the struct fields

		nameField := valueOfPerson.FieldByName("Name");
		ageField := valueOfPerson.FieldByName("Age");

		if nameField.CanSet() {
			nameField.SetString("Bob");
		}
		if ageField.CanSet() {
			ageField.SetInt(30);
		}
		fmt.Println(person);
    }
   ```
   * The `FieldByName` is used to get the field by its name.
   * The `CanSet()` checks if the value of the field can be modified.
   *  The methods `SetString()` and `SetInt()` are used to modify the value of a field.

*  **Calling functions with reflection**
	```go
	package main

    import (
		"fmt"
        "reflect"
	)
	
	func Add(a, b int) int {
		return a + b;
	}
	
    func main() {
		addFunc := reflect.ValueOf(Add);
		
		values := []reflect.Value{reflect.ValueOf(10), reflect.ValueOf(20)};
		
		results := addFunc.Call(values);
		
		fmt.Println(results[0].Int()); // Output: 30
    }
   ```
	*  The `reflect.ValueOf` can receive a function and return a `reflect.Value`.
	* The method `Call` receives a slice with the arguments of the function.
	*   The example above shows how to call a function using reflection.

**14.2 Use Cases for Reflection**

While reflection should be used carefully and is generally not needed for most common cases, it can be useful in specific scenarios:

*   **Generic Data Handling:** Reflection is useful when dealing with data structures where the type is not known until runtime, such as in generic serialization or deserialization libraries.
*   **Dynamic Invocation:** Reflection is used in frameworks where methods need to be invoked at runtime based on data or user input, such as dependency injection systems.
*   **Testing Frameworks:** Reflection is often used in testing frameworks to inspect structures and automatically compare their values.
*   **Tooling and Code Generation:** Code generation and tooling often rely on reflection to understand and generate code based on existing data structures and types.
*   **Interacting with External Libraries:** Sometimes you might need to use reflection when dealing with external libraries and frameworks.

**Important Considerations:**

*   **Performance Overhead:** Reflection has a performance cost, so avoid using it in performance-critical code.
*   **Reduced Type Safety:** Reflection can bypass type checking, which can lead to runtime errors and harder-to-debug code.
*   **Maintainability:** Reflection can make code harder to read and understand, potentially reducing the maintainability of your codebases.

**14.3 Key Takeaways**

This chapter has provided an introduction to reflection in Go:

*   **Reflection:** Allows Go programs to examine and manipulate their own structure at runtime.
*   **`reflect` Package:** The core package providing reflection functionality (`reflect.Type` and `reflect.Value`).
*   **Type and Value:** The `reflect.TypeOf` and `reflect.ValueOf` are used to get the `reflect.Type` and `reflect.Value`.
*  **Access Fields:** You can access and modify struct fields by using the `reflect.Value` methods.
*  **Dynamic function calls**: You can call functions by using reflection, if you know their name at runtime.
*   **Use Cases:** Reflection is useful in specific scenarios, such as generic data handling and dynamic invocation.
*   **Considerations:** Be aware of performance costs, reduced type safety, and maintainability implications when using reflection.

Understanding reflection allows you to write highly dynamic Go programs, but you must be aware that its usage must be done carefully and only when required.

