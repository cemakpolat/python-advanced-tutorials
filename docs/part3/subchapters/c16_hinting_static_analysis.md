---
title: Advanced Type Hinting and Static Analysis
parent: Applying Advanced Concepts 
nav_order: 6
---

## Chapter 16: Advanced Type Hinting and Static Analysis


*   **16.1 Introduction to Type Hinting (Revisited):**

    Type hinting, introduced in Python 3.5, provides a way to add optional type annotations to your code. While Python remains a dynamically typed language (type checking occurs at runtime), type hints allow you to express the expected data types of variables, function arguments, and return values. This opens the door to static analysis tools that can catch type errors *before* you run your code, leading to more robust and maintainable software.

    Benefits of type hinting:

    *   **Improved Code Readability:** Type hints make your code easier to understand by explicitly specifying the expected data types.
    *   **Early Error Detection:** Static analysis tools like `mypy` can detect type errors before you run your code, saving you time and effort in debugging.
    *   **Better IDE Support:** Type hints enable IDEs to provide better code completion, error checking, and refactoring support.
    *   **Enhanced Collaboration:** By adding type hints, it makes it easier to transfer the code and make future changes and it adds value to the code in general.

    Basic type hints:

    *   `int`: Represents an integer.
    *   `str`: Represents a string.
    *   `float`: Represents a floating-point number.
    *   `bool`: Represents a boolean value.
    *   `List[T]`: Represents a list of elements of type `T`.
    *   `Tuple[T1, T2, ...]`: Represents a tuple with elements of types `T1`, `T2`, etc.
    *   `Dict[K, V]`: Represents a dictionary with keys of type `K` and values of type `V`.
    *   `Set[T]`: Represents a set of elements of type `T`.
    *   `Optional[T]`: Represents a value that can be either of type `T` or `None`.
    *   `Union[T1, T2, ...]`: Represents a value that can be of any of the types `T1`, `T2`, etc.

    Type hints for functions:

    ```python
    def add(x: int, y: int) -> int: #Argument types are expected to be int and it returns a int
        return x + y
    ```

    Type hints for variables:

    ```python
    age: int = 30
    name: str = "Alice"
    ```

*   **16.2 Advanced Type Hints:**

    Beyond the basic type hints, Python's `typing` module provides several advanced type hints that allow you to express more complex type relationships.

    *   **`Any`:**

        The `Any` type hint indicates that a variable can be of any type. This is useful when you don't know the type of a variable or when you want to allow a variable to be of multiple types.
        Avoid using it, this could affect the type validation that it's intented here.

        ```python
        from typing import Any

        def process_data(data: Any):
            print(data) # It could be a str, int or other thing
        ```

    *   **`Callable`:**

        The `Callable` type hint is used to specify the type of a callable object, such as a function or method. You can use `Callable` to specify the argument types and return type of the callable.

        ```python
        from typing import Callable

        def apply_function(func: Callable[[int, int], int], x: int, y: int) -> int:
            return func(x, y)

        def add(x: int, y: int) -> int:
            return x + y

        result = apply_function(add, 2, 3)
        print(result)
        ```

    *   **`Generic` and Type Variables:**

        Generic types allow you to define classes and functions that work with multiple types. You can use the `Generic` class from the `typing` module to create generic classes, and you can use type variables to represent the type parameters.

        ```python
        from typing import Generic, TypeVar

        T = TypeVar('T') #Generic Types
        U = TypeVar('U') #Generic Types

        class Box(Generic[T]): #You can add more than 1 value
            def __init__(self, content: T):
                self.content = content

            def get_content(self) -> T:
                return self.content
            #You can combine functions as well
            def get_type(self) -> type[T]: #Check that the Type is the same
                return type(self.content)

        box_int = Box[int](10)
        print(box_int.get_content())
        print(box_int.get_type())
        ```

    *   **`TypeVar`:** How to implement Type hints in the methods.

        This allows developers to enforce the type at the moment it is created

    *   **`Protocol` (Structural Subtyping):**

        Protocols define shared method signatures across classes, enforcing consistency without inheritance. They enable structural subtyping (or static duck-typing), where type compatibility is determined by matching method names and signatures.

        ```python
        from typing import Protocol

        class SupportsClose(Protocol):
            def close(self) -> None:
                ...

        def close_if_possible(obj: SupportsClose):
            obj.close() #Ensure that it supports the method

        class MyFile:
            def close(self):
                print('close!')

        my_file = MyFile()
        close_if_possible(my_file)
        ```

    *   **`Literal`:**

        The `Literal` type hint allows you to specify a limited set of allowed values for a variable. This can be useful for enforcing constraints on function arguments or class attributes.

        ```python
        from typing import Literal

        def set_log_level(level: Literal["DEBUG", "INFO", "WARNING", "ERROR"]):
            print(f"Setting log level to {level}")

        set_log_level("INFO") # Valid
        #set_log_level("BLAH")  # type checker will flag this as an error
        ```

    *   **`TypedDict`:**

        The `TypedDict` type hint allows you to create dictionary types with specific key-value types. This can be useful for validating the structure of dictionaries and providing better type information.

        ```python
        from typing import TypedDict

        class Movie(TypedDict):
            name: str
            year: int
            cast: list[str]

        def print_movie_cast(movie: Movie):
            print(movie['cast'])

        my_movie: Movie = {'name': 'The Matrix', 'year': 1999, 'cast': ['Keanu Reeves']}
        print_movie_cast(my_movie) # Correct

        ```

*   **16.3 Custom Type Hints:**

    Beyond the built in Typehints, you can create your own type hints.

    *   **Creating Custom Type Aliases for Complex Types:**

        Type aliases allow you to give a name to a complex type, making your code more readable and maintainable.

        ```python
        from typing import List, Tuple

        Point = Tuple[int, int]
        Color = Tuple[int, int, int]
        Grid = List[List[int]]
        ```

    *   **Defining Custom Protocols for Structural Subtyping:**

        You can define your own protocols to specify the required methods and attributes for a type. This is useful for enforcing interfaces and ensuring that objects have the expected behavior.

        ```python
        from typing import Protocol

        class Closer(Protocol):
            def close(self) -> None:
                ...
        ```

    *   **Using `NewType` to Create Distinct Types with the Same Underlying Representation:**

        `NewType` allows you to create distinct types with the same underlying representation. This can be useful for preventing errors caused by accidentally using a value of the wrong type.

        ```python
        from typing import NewType

        UserId = NewType('UserId', int) #Just int with another meaning

        def get_user_name(user_id: UserId) -> str:
             # you only can call with UserID
             return f"User {user_id}"

        user_id: UserId = UserId(123)
        name = get_user_name(user_id)

        print(name) #print correct User 123

        #you can not do this int id = 123 because it has to be explicit UserID
        #user_id: int = 123 #This will raise Type error

        int_id = 123 #Wrong usage will not raise
        #get_user_name(int_id) #This will raise type error
        ```


*   **16.4 Static Analysis with `mypy`:**

    Static analysis is the process of examining code *without* executing it. This allows you to catch many types of errors, enforce coding standards, and identify potential security vulnerabilities early in the development process. `mypy` is a popular static type checker for Python that uses type hints to detect type errors.

    *   **What is Static Analysis?** Detecting errors and enforcing coding standards without running the code.

    *   **Installing and Configuring `mypy`:**

        ```bash
        pip install mypy
        ```

    *   **Running `mypy` on Your Code:**

        To run `mypy` on your code, simply pass the name of the Python file or directory to the `mypy` command:

        ```bash
        mypy your_module.py
        ```

    *   **Understanding `mypy` Error Messages:**

        `mypy` error messages provide information about the type error, the location of the error, and the expected type.

    *   **Configuring `mypy` with a `mypy.ini` File:**

        You can configure `mypy` using a `mypy.ini` file. This allows you to specify various options, such as the Python version, the strictness of type checking, and the directories to include or exclude from analysis.

        ```ini
        # mypy.ini

        [mypy]
        python_version = 3.9
        strict = True
        ignore_missing_imports = True
        ```

    *   **Using `mypy` to Enforce Type Hints and Detect Type Errors:**

        By adding type hints to your code and running `mypy`, you can catch type errors before you run your code, improving the reliability and maintainability of your applications.

*   **16.5 Other Static Analysis Tools:**

    While `mypy` focuses on type checking, other static analysis tools can help you identify style errors, potential bugs, and security vulnerabilities.

    *   **`pylint`:**

        `pylint` is a comprehensive code analysis tool that checks for style errors, potential bugs, and security vulnerabilities. It can be customized with a variety of plugins and rules.

        ```bash
        pip install pylint
        pylint your_module.py
        ```

    *   **`flake8`:**

        `flake8` is a simpler code style checker that enforces PEP 8 and other style guidelines. It is faster and easier to use than `pylint`, but it provides fewer features.

        ```bash
        pip install flake8
        flake8 your_module.py
        ```

    *   **`bandit`:**

        `bandit` is a security linter that checks for common security vulnerabilities in Python code. It can identify potential issues such as:

        *   Hardcoded passwords
        *   Use of insecure functions
        *   SQL injection vulnerabilities
        *   XSS vulnerabilities

        ```bash
        pip install bandit
        bandit your_module.py
        ```
    *   `pyright`:
         Pyright is a static type checker created by Microsoft
*   **16.6 Integrating Type Hinting and Static Analysis into Your Workflow:**

    Type hinting and static analysis are most effective when integrated into your development workflow.

    *   **Using Type Hints and Static Analysis in Conjunction with Unit Tests:**

        Type hints and static analysis can help you catch type errors that might not be caught by unit tests. Unit tests can then be used to verify the behavior of your code under different conditions.

    *   **Integrating Static Analysis into Your CI/CD Pipeline:**

        You can integrate static analysis into your CI/CD pipeline to automatically check your code for type errors and style violations whenever you commit changes. This can help you to catch errors early in the development process and prevent them from making their way into production.

        Example with GitHub Actions:

        ```yaml
        name: Static Analysis

        on: [push, pull_request]

        jobs:
          mypy:
            runs-on: ubuntu-latest
            steps:
              - uses: actions/checkout@v3
              - name: Set up Python 3.9
                uses: actions/setup-python@v4
                with:
                  python-version: "3.9"
              - name: Install dependencies
                run: |
                  python -m pip install --upgrade pip
                  pip install mypy
              - name: Run mypy
                run: mypy .
        ```

    *   **Code Reviews and Type Hint Enforcement:**

        Code reviews are an important part of the software development process. During code reviews, you can check that the code is well-typed and that type hints are being used correctly.

*   **Project 16.1: Refactoring a Codebase with Type Hints and Static Analysis**

    Take an existing codebase (or a portion of it) that does not have type hints and add type hints to all functions, classes, and variables. Use `mypy` to identify and fix any type errors. Refactor the code to improve its readability and maintainability.
    As said before here you must provide code from your side or from a open repository to improve their implementation with type hints

   ```python
   import os
   from typing import List, Tuple, Dict

   class FileProcessor:
       def __init__(self, input_dir: str, output_dir: str) -> None:
           """Initializes the FileProcessor with input and output directories."""
           self.input_dir = input_dir
           self.output_dir = output_dir

       def list_files(self, extension: str) -> List[str]:
           """Lists all files with a given extension in the input directory."""
           files = []
           for filename in os.listdir(self.input_dir):
               if filename.endswith(extension):
                   files.append(os.path.join(self.input_dir, filename))
           return files

       def process_file(self, filepath: str) -> Tuple[str, int]:
            """Reads a file, counts the number of lines, and returns the filename and line count."""
            try:
                with open(filepath, 'r') as f:
                    lines = f.readlines()
                line_count = len(lines)
                return filepath, line_count
            except FileNotFoundError:
                return filepath, -1

       def write_output(self, data: List[Tuple[str, int]], output_filename: str) -> None:
            """Writes the processed data to an output file."""
            output_path = os.path.join(self.output_dir, output_filename)
            with open(output_path, 'w') as f:
                for filepath, line_count in data:
                    f.write(f"{filepath}: {line_count}\n")

   # Example Usage
   if __name__ == "__main__":
       # Before using, you will need to create in the same path folder an example of input and output example
       # The code does not validate the current implementation to avoid to be long
       input_dir = "input_directory"  #Directory
       output_dir = "output_directory"  # Directory

       #Validate the directory and files are created, but the main aim is to exemplify the usage of Type Hints
       processor = FileProcessor(input_dir, output_dir)
       files = processor.list_files(".txt")

       processed_data = []
       for file in files:
           filepath, line_count = processor.process_file(file)
           processed_data.append((filepath, line_count))

       processor.write_output(processed_data, "output.txt")

       print("Code completed!")
   ```
*   **16.7 Conclusion:**

    This chapter provided a comprehensive overview of advanced type hinting techniques in Python and how to leverage static analysis tools to improve code quality, readability, and maintainability. It's important to add all of this code to the github since it will make it easy for users to get and check the improvements.

