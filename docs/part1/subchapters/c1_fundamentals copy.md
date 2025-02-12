---
title: Python Fundamentals
parent: Solidify Python Fundamentals
nav_order: 1
---
## Chapter 1: Python Fundamentals - A Concise Review

*   **1.1 Introduction:**

    This chapter serves as a rapid refresh of the Python fundamentals. We assume you possess a solid working knowledge of Python; our aim here is to ensure we're all on the same page regarding core syntax and concepts before we delve into more sophisticated topics. Think of it as a quick pit stop to check the tires and top off the fuel before hitting the open road of advanced Python programming. If you find yourself struggling with the material in this chapter, it might be beneficial to revisit some introductory Python resources before proceeding.

*   **1.2 Data Types:**

    Python's rich set of built-in data types are the building blocks of any program. Let's take a brisk tour, focusing on the nuances often overlooked.

    *   **Numbers:** Python supports integers (`int`), floating-point numbers (`float`), and complex numbers (`complex`). While straightforward, it's worth remembering the subtleties of integer division and floating-point representation.

        *   Integer division using the `//` operator performs floor division, discarding any fractional part. For example, `7 // 3` evaluates to `2`. This behavior is crucial in situations where you need to work with whole numbers, such as array indexing or counting iterations.

        *   Floating-point numbers, represented internally using binary fractions, can sometimes lead to unexpected results due to precision limitations. For instance, `0.1 + 0.2` might not be exactly equal to `0.3` due to rounding errors. While often negligible, these errors can become significant in financial calculations or scientific simulations requiring high accuracy. In such cases, the `decimal` module provides a way to work with decimal numbers with arbitrary precision. Consider:

            ```python
            from decimal import Decimal

            a = Decimal('0.1')
            b = Decimal('0.2')
            result = a + b
            print(result)  # Output: 0.3
            ```

        *   Python also provides bitwise operators (`&`, `|`, `^`, `~`, `<<`, `>>`) that operate on integers at the bit level. While less commonly used in general application development, they are valuable for low-level programming, data compression, and certain types of algorithm optimization.

    *   **Strings:** Strings (`str`) in Python are immutable sequences of Unicode characters. Understanding Unicode is vital for handling text from diverse languages correctly.

        *   Python 3 uses UTF-8 as the default encoding for strings. This means that you can seamlessly work with characters from virtually any language without worrying about encoding issues. However, it's still important to be aware of encoding when reading or writing files, especially if they were created using a different encoding.

        *   Formatted string literals (f-strings), introduced in Python 3.6, provide a concise and readable way to embed expressions within strings. For example:

            ```python
            name = "Alice"
            age = 30
            message = f"Hello, my name is {name} and I am {age} years old."
            print(message)
            ```

            F-strings not only simplify string formatting but also allow you to directly execute expressions within the string, making them a powerful tool for generating dynamic text.

        *   The `re` module provides powerful regular expression operations. While a full treatment of regular expressions is beyond the scope of this chapter, understanding the basics of pattern matching and substitution is essential for many text processing tasks. We'll touch on these later in the project sections of this book.

    *   **Booleans:** Booleans (`bool`) represent truth values: `True` or `False`. Python's concept of "truthiness" extends beyond just `True` and `False`.

        *   Many values are implicitly considered `False` in a boolean context: `0`, `0.0`, `""` (empty string), `[]` (empty list), `()` (empty tuple), `{}` (empty dictionary), `set()` (empty set), and `None`. All other values are generally considered `True`. This implicit conversion is often used in conditional statements and can make your code more concise.  For example:

            ```python
            my_list = []
            if not my_list: # more Pythonic than if len(my_list) == 0
                print("The list is empty")
            ```

    *   **Collections:** Python offers a rich set of built-in collection types for storing and organizing data. Understanding the characteristics of each type is key to writing efficient and maintainable code.

        *   **Lists:** Lists (`list`) are mutable, ordered sequences of items.

            *   List comprehensions provide a concise way to create new lists based on existing iterables. They are a fundamental Pythonic idiom and should be used whenever possible.  For instance:

                ```python
                numbers = [1, 2, 3, 4, 5]
                squares = [x**2 for x in numbers] # concise and readable
                print(squares) # Output: [1, 4, 9, 16, 25]
                ```

            *   Slicing allows you to extract portions of a list. Advanced slicing features, such as using a step size, can be powerful:

                ```python
                numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
                even_numbers = numbers[::2] # every other element, starting from the beginning
                print(even_numbers) # Output: [0, 2, 4, 6, 8]
                reversed_numbers = numbers[::-1] # creates a reversed copy of the list
                print(reversed_numbers) # Output: [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]
                ```

        *   **Tuples:** Tuples (`tuple`) are immutable, ordered sequences of items. Their immutability makes them suitable for situations where you need to ensure that the data remains unchanged.

            *   Tuples can be used as keys in dictionaries, something lists cannot do because of their mutability.

            *   Tuple unpacking allows you to assign elements of a tuple to individual variables in a single statement:

                ```python
                point = (10, 20)
                x, y = point # assigns 10 to x and 20 to y
                print(x, y) # Output: 10 20
                ```
    *   **Dictionaries:** Dictionaries (`dict`) are mutable collections that store key-value pairs.

        *   Dictionary comprehensions create new dictionaries in a similar way to List comprehensions.
        *   `dict.get(key, default)` efficiently retrieves the value associated with a key, returning `default` (which defaults to `None`) if the key doesn't exist.
        *   `dict.setdefault(key, default)` assigns default if the `key` does not exists
        *   Dictionary views provide a dynamic view of the dictionary's keys, values, or items. They are not copies, so changes to the dictionary are reflected in the view.

*   **1.3 Control Flow:**
    Python uses colon and indentation to create code blocks. `if` statements use condition to execute the code. `for` loop can be used to iterate over different data structures.

    ```python
    numbers = [1, 2, 3, 4, 5]

    for index, number in enumerate(numbers):
        print(f"Index: {index}, Number: {number}")
    ```

    In the above example `enumerate` is used to get both the index and the value. `zip()` function is used to iterate over multiple sequences in parallel. `break`, `continue` and `else` are used in loops. Exception Handling is done with `try`, `except`, `finally`, `raise`.

*   **1.4 Functions:**

    Functions are the workhorses of Python code, encapsulating reusable blocks of logic. A deep understanding of how functions work is essential for writing modular, maintainable, and testable programs.

    *   Functions are defined using the `def` keyword, followed by the function name, a list of parameters in parentheses, and a colon. The function body is indented.

    *   Python supports several types of arguments:

        *   *Positional arguments* are passed to the function in the order they are defined.

        *   *Keyword arguments* are passed to the function by explicitly specifying the parameter name along with the value (e.g., `my_function(name="Alice", age=30)`). Keyword arguments can be specified in any order after all positional arguments have been provided.

        *   *Default argument values* allow you to specify a default value for a parameter that will be used if the caller does not provide a value for that parameter. **Important Note:** Be extremely cautious when using mutable objects (lists, dictionaries) as default argument values. The default value is created only once when the function is defined, and subsequent calls to the function will share the same mutable object. This can lead to unexpected behavior if you modify the default value within the function. For example:

            ```python
            def append_to_list(item, my_list=[]): # Dangerous!
                my_list.append(item)
                return my_list

            list1 = append_to_list(1)
            print(list1)  # Output: [1]
            list2 = append_to_list(2)
            print(list2)  # Output: [1, 2]  <-- Unexpected!
            ```

            The correct way to handle this is to use `None` as the default value and create a new list inside the function if `None` is provided:

            ```python
            def append_to_list(item, my_list=None): # Correct!
                if my_list is None:
                    my_list = []
                my_list.append(item)
                return my_list
            ```

        *   `*args` and `**kwargs` provide a way to pass a variable number of arguments to a function. `*args` collects any extra positional arguments into a tuple, while `**kwargs` collects any extra keyword arguments into a dictionary. These are powerful tools for creating flexible functions that can handle a wide range of input. For example:

            ```python
            def my_function(arg1, arg2, *args, **kwargs):
                print(f"arg1: {arg1}, arg2: {arg2}")
                print(f"args: {args}")
                print(f"kwargs: {kwargs}")

            my_function(1, 2, 3, 4, 5, name="Alice", age=30)
            ```

            This is useful to wrap a function, and then add logging

            ```python
            def my_wrapper(func, *args, **kwargs):
                print('do pre-processing here')
                func(*args, **kwargs)
                print('do post-processing here')
            ```
    *   Functions can return multiple values by returning a tuple. For example:

        ```python
        def get_coordinates():
            x = 10
            y = 20
            return x, y # returns a tuple (x, y)

        x, y = get_coordinates() # tuple unpacking
        print(f"x: {x}, y: {y}")
        ```

    *   Lambda functions are anonymous functions that can be defined using the `lambda` keyword. They are typically used for short, simple functions that can be defined in a single line. For example:

        ```python
        square = lambda x: x**2
        print(square(5))  # Output: 25

        numbers = [1, 2, 3, 4, 5]
        squares = list(map(lambda x: x**2, numbers)) # Using a lambda with map()
        print(squares) # Output: [1, 4, 9, 16, 25]
        ```

    *   Docstrings (documentation strings) are multiline strings used to document functions, classes, modules, and methods. They are enclosed in triple quotes (`"""Docstring goes here"""`) and should provide a clear and concise description of the object's purpose, arguments, and return values. You can access the docstring of an object using the `help()` function or the `__doc__` attribute.

        ```python
        def my_function(x, y):
            """This function adds two numbers together.

            Args:
                x: The first number.
                y: The second number.

            Returns:
                The sum of x and y.
            """
            return x + y

        help(my_function) # Displays the docstring
        print(my_function.__doc__) # Also displays the docstring
        ```

    *   Type hints are annotations that specify the expected data types of function arguments and return values. Type hints are not enforced by the Python interpreter at runtime, but they can be used by static type checkers (e.g., `mypy`) to detect type errors before you run your code. Type hints can significantly improve code readability and maintainability, especially in large and complex projects.

        ```python
        from typing import List, Tuple

        def process_data(data: List[int]) -> Tuple[float, float]:
            """Calculates the mean and standard deviation of a list of numbers.

            Args:
                data: A list of integers.

            Returns:
                A tuple containing the mean and standard deviation.
            """
            # ... implementation ...
            return 0.0, 0.0 # Dummy return for example
        ```

        We'll delve deeper into type hints in a later chapter. For now, focus on understanding the basic syntax.

*   **1.5 Modules and Packages:**

    Modules and packages are fundamental for organizing and reusing Python code. They allow you to break down large programs into smaller, more manageable pieces, and to share code between different projects.

    *   Modules are simply Python files containing definitions and statements. You can import modules using the `import` statement. There are several ways to import modules:

        *   `import math`: Imports the entire `math` module. You must then refer to objects within the module using the module name (e.g., `math.sqrt(16)`).

        *   `import math as m`: Imports the `math` module and assigns it the alias `m`. This can be useful for shortening long module names (e.g., `m.sqrt(16)`).

        *   `from math import sqrt`: Imports only the `sqrt` function from the `math` module. You can then use the `sqrt` function directly without specifying the module name (e.g., `sqrt(16)`).

        *   `from math import *`: Imports all names from the `math` module. **Caution:** This is generally discouraged because it can pollute your namespace and make it difficult to determine where names are defined. It's best to import only the names that you need explicitly.

    *   Packages are a way of organizing related modules into a directory hierarchy. A package is simply a directory containing one or more Python modules and a special file named `__init__.py`. The `__init__.py` file can be empty, or it can contain code that is executed when the package is imported.

    *   Even if `__init__.py` is empty, it is needed to tell Python that the directory should be treated as a package

    *   The `__name__ == "__main__"` conditional is a common idiom used in Python modules. When a Python module is run as a standalone script, the `__name__` variable is set to `"__main__"`. When a module is imported into another module, the `__name__` variable is set to the name of the module. This allows you to write code that is executed only when the module is run as a standalone script, but not when it is imported into another module. For example:

        ```python
        # my_module.py

        def my_function():
            print("Hello from my_module!")

        if __name__ == "__main__":
            my_function() # This will only be executed if my_module.py is run directly
        ```

        If you run `python my_module.py`, the output will be "Hello from my_module!". If you import `my_module` into another script, the `my_function()` will *not* be executed automatically.

*   **1.6 Working with Files:**

    Being able to read and write files is crucial for interacting with data and external systems. Python provides simple and powerful mechanisms for file I/O (input/output).

    *   The `open()` function is used to open a file. It takes two main arguments: the filename and the mode. The mode specifies how the file will be opened (e.g., read, write, append). Common modes include:

        *   `'r'`: Read mode (default). Opens the file for reading.
        *   `'w'`: Write mode. Opens the file for writing. If the file already exists, it will be overwritten. If the file does not exist, it will be created.
        *   `'a'`: Append mode. Opens the file for writing, but appends to the end of the file if it exists. If the file does not exist, it will be created.
        *   `'x'`: Exclusive creation mode. Opens the file for writing, but only if the file does not already exist. If the file exists, an error will be raised.
        *   `'b'`: Binary mode. Opens the file in binary mode (e.g., for reading or writing images or other binary data).
        *   `'t'`: Text mode (default). Opens the file in text mode (e.g., for reading or writing text files).
        *   `'+'`: Update mode. Opens the file for both reading and writing.

        You can combine modes. For example, `'rb'` opens a file in binary read mode.

    *   Once a file is open, you can read data from it using the `read()`, `readline()`, and `readlines()` methods:

        *   `read()`: Reads the entire contents of the file into a single string.  Use this with caution for large files, as it can consume a lot of memory.

        *   `readline()`: Reads a single line from the file, including the newline character (`\n`).

        *   `readlines()`: Reads all lines from the file into a list of strings.

        To write data to a file, you can use the `write()` and `writelines()` methods:

        *   `write(string)`: Writes a string to the file.
        *   `writelines(list_of_strings)`: Writes a list of strings to the file. It does *not* automatically add newlines, so you'll need to include them in the strings if desired.

        Example:

        ```python
        # Writing to a file
        with open("my_file.txt", "w") as f:
            f.write("Hello, world!\n")
            f.writelines(["This is line 2.\n", "This is line 3.\n"])

        # Reading from a file
        with open("my_file.txt", "r") as f:
            # Read the whole file
            #content = f.read()
            #print(content)

            # Read line by line
            #line = f.readline()
            #print(line)

            # Read all the lines
            lines = f.readlines()
            print(lines)
        ```

    *   The `with` statement is *essential* for working with files. It ensures that the file is properly closed automatically, even if exceptions occur. This prevents resource leaks and data corruption. The `with` statement works by using *context managers*, a more advanced Python feature we'll explore later. When the `with` block is exited, the context manager's `__exit__` method is called, which in the case of file objects, closes the file. Always use `with` when working with files!

*   **1.7 Conclusion:**

    This chapter provided a whirlwind tour of Python fundamentals, covering data types, control flow, functions, modules, and file I/O. While we've only scratched the surface of each topic, the concepts presented here form the bedrock upon which more advanced Python programming is built. A solid grasp of these fundamentals is crucial for understanding the material covered in the subsequent chapters.

    In the next chapter, we'll dive into object-oriented programming (OOP) in Python, exploring classes, inheritance, polymorphism, and other key OOP concepts. We'll see how OOP can be used to create more modular, reusable, and maintainable code. We will also implement a project to reinforce the key concepts.