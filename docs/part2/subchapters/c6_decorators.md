---
title: Decorators: Enhancing Functions and Classes
parent: Advanced Python Concepts
nav_order: 5
---


## Chapter 6: Decorators: Enhancing Functions and Classes**

*   **6.1 Introduction to Decorators:**

    Decorators are a powerful and elegant feature in Python that allows you to modify the behavior of functions and classes in a clean and reusable way. They provide a way to "wrap" a function or class with additional code, without directly modifying its original source.

    Why use decorators? They promote:

    *   **Code Reuse:** Decorators can be applied to multiple functions or classes, eliminating the need to repeat the same code in multiple places.
    *   **Separation of Concerns:** Decorators allow you to separate cross-cutting concerns (e.g., logging, timing, authentication) from the core logic of your functions and classes.
    *   **Improved Readability:** Decorators can make your code more readable by reducing boilerplate and highlighting the core functionality of your functions and classes.

    The decorator syntax is simple and intuitive: `@decorator_name`. You place the `@decorator_name` line immediately before the function or class definition that you want to decorate.

    Before diving into decorators, it's important to remember that functions are first-class objects in Python. This means that you can treat functions like any other object: you can assign them to variables, pass them as arguments to other functions, and return them from functions. This flexibility is essential for understanding how decorators work.

*   **6.2 Function Decorators:**

    A function decorator is a function that takes another function as an argument, modifies its behavior, and returns the modified function.

    Here's a simple example of a function decorator that logs function calls:

    ```python
    def log_calls(func):
        def wrapper(*args, **kwargs):
            print(f"Calling {func.__name__} with arguments: {args}, {kwargs}")
            result = func(*args, **kwargs)
            print(f"{func.__name__} returned: {result}")
            return result
        return wrapper

    @log_calls # Apply the decorator
    def add(x, y):
        return x + y

    print(add(2, 3))
    ```

    **Explanation:**

    1.  **`log_calls(func)`:** This is the decorator function. It takes a function `func` as an argument.
    2.  **`wrapper(*args, **kwargs)`:** This is the inner function that *replaces* the original function. It takes any number of positional arguments (`*args`) and keyword arguments (`**kwargs`).
    3.  **`print(f"Calling {func.__name__}...`:** This logs the function call, including the function name and arguments.
    4.  **`result = func(*args, **kwargs)`:** This calls the original function with the arguments it received.
    5.  **`print(f"{func.__name__} returned...`:** This logs the return value of the original function.
    6.  **`return result`:** This returns the return value of the original function.
    7.  **`return wrapper`:** The `log_calls` function returns the `wrapper` function.
    8.  **`@log_calls`:** This applies the `log_calls` decorator to the `add` function. It's equivalent to `add = log_calls(add)`.

    When you call the `add` function, you're actually calling the `wrapper` function, which logs the function call, calls the original `add` function, logs the return value, and returns the result.

    **Preserving Function Metadata with `functools.wraps`:**

    When you decorate a function, you lose the original function's metadata (e.g., `__name__`, `__doc__`). The `functools.wraps` decorator can be used to preserve this metadata:

    ```python
    import functools

    def log_calls(func):
        @functools.wraps(func) # Preserves metadata
        def wrapper(*args, **kwargs):
            """Wrapper function for logging calls.""" # Docstring is now visible
            print(f"Calling {func.__name__} with arguments: {args}, {kwargs}")
            result = func(*args, **kwargs)
            print(f"{func.__name__} returned: {result}")
            return result
        return wrapper

    @log_calls
    def add(x, y):
        """Adds two numbers together.""" # Original docstring preserved
        return x + y

    print(add.__name__) # Output: add
    print(add.__doc__) # Output: Adds two numbers together.
    ```

    Using `functools.wraps` is essential for preserving the identity of the decorated function and ensuring that it behaves as expected.

*   **6.3 Class Decorators:**

    A class decorator is a function that takes a class as an argument, modifies its behavior, and returns the modified class. Class decorators can be used to add methods to a class, modify existing methods, or change the class's inheritance hierarchy.

    Here's an example of a class decorator that adds a method to a class:

    ```python
    def add_method(method):
        def wrapper(cls):
            setattr(cls, method.__name__, method)
            return cls
        return wrapper

    def say_hello(self):
        return "Hello!"

    @add_method(say_hello)
    class MyClass:
        pass

    obj = MyClass()
    print(obj.say_hello()) # Output: Hello!
    ```

    Here's another example of a class decorator that implements the Singleton pattern:

    ```python
    def singleton(cls):
        instances = {}
        def get_instance(*args, **kwargs):
            if cls not in instances:
                instances[cls] = cls(*args, **kwargs)
            return instances[cls]
        return get_instance

    @singleton
    class MyClass:
        def __init__(self, value):
            self.value = value

    obj1 = MyClass(10)
    obj2 = MyClass(20) # will not create a new instance

    print(obj1 is obj2)  # Output: True
    print(obj1.value) #Output: 10
    print(obj2.value) #Output: 10
    ```

    **Class Decorators vs. Metaclasses:**

    Class decorators and metaclasses can both be used to modify classes, but they operate at different stages of the class definition process.

    *   Class decorators modify an existing class after it has been defined.
    *   Metaclasses control the creation of a class.

    Class decorators are generally simpler to use than metaclasses, but they are less powerful. Metaclasses provide more fine-grained control over the class definition process.

*   **6.4 Decorator Use Cases:**

    Decorators can be used for a wide variety of purposes. Here are some common use cases:

    *   **Logging:** Logging function calls, arguments, and return values.
    *   **Timing:** Measuring the execution time of functions.
    *   **Authentication and Authorization:** Checking user permissions before executing a function.
    *   **Caching:** Caching the results of expensive function calls.
    *   **Input Validation:** Validating function arguments.
    *   **Retry Logic:** Retrying failed function calls.

*   **6.5 Parameterized Decorators:**

    A parameterized decorator is a decorator that accepts arguments. This allows you to customize the behavior of the decorator based on the specific needs of the function or class being decorated.

    To create a parameterized decorator, you define a function that takes the decorator arguments as parameters and returns a decorator function. The decorator function then takes the function to be decorated as an argument and returns the modified function.

    Here's an example of a parameterized decorator that logs function calls to a specific file:

    ```python
    import functools

    def log_to_file(filename):
        def decorator(func):
            @functools.wraps(func)
            def wrapper(*args, **kwargs):
                with open(filename, "a") as f:
                    f.write(f"Calling {func.__name__} with arguments: {args}, {kwargs}\n")
                    result = func(*args, **kwargs)
                    f.write(f"{func.__name__} returned: {result}\n")
                return result
            return wrapper
        return decorator

    @log_to_file("my_log.txt")
    def my_function(x, y):
        return x + y

    my_function(1, 2) # Logs to my_log.txt
    ```

    Here's an example of a parameterized decorator that retries a function a specified number of times:

    ```python
    import time
    import functools

    def retry(num_attempts, delay=1):
        def decorator(func):
            @functools.wraps(func)
            def wrapper(*args, **kwargs):
                for i in range(num_attempts):
                    try:
                        return func(*args, **kwargs)
                    except Exception as e:
                        print(f"Attempt {i + 1} failed: {e}")
                        time.sleep(delay)
                print(f"Function {func.__name__} failed after {num_attempts} attempts")
                return None # Or raise the exception
            return wrapper
        return decorator

    @retry(num_attempts=3, delay=2)
    def flaky_function():
        """This function sometimes fails."""
        if random.random() < 0.5:
            raise ValueError("Function failed")
        return "Function succeeded"
    ```

*   **6.6 Decorator Chaining:**

    You can apply multiple decorators to a single function or class. This is known as decorator chaining. The decorators are applied from bottom to top.

    ```python
    import functools
    import time

    def log_calls(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            print(f"Calling {func.__name__} with arguments: {args}, {kwargs}")
            result = func(*args, **kwargs)
            print(f"{func.__name__} returned: {result}")
            return result
        return wrapper

    def timer(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            start_time = time.time()
            result = func(*args, **kwargs)
            end_time = time.time()
            print(f"{func.__name__} took {end_time - start_time:.4f} seconds to execute")
            return result
        return wrapper

    @log_calls
    @timer
    def my_function(x, y):
        time.sleep(1) #Simulate something
        return x + y

    my_function(1, 2) # Both log_calls and timer will be applied
    ```

    In this example, the `timer` decorator is applied first, and then the `log_calls` decorator is applied. This means that the `log_calls` decorator will log the call to the `timer` decorator's `wrapper` function, rather than the original `my_function`.

*   **6.7 Best Practices for Decorators:**

    *   **Keep decorators simple and focused.** Decorators should perform a single, well-defined task. Avoid complex logic or side effects.
    *   **Document your decorators clearly.** Explain the purpose of the decorator, how it works, and any arguments it accepts.
    *   **Use `functools.wraps` to preserve function metadata.** This is essential for maintaining the identity of the decorated function.
    *   **Avoid side effects in decorators.** Decorators should not modify the state of the object or perform any other actions that could have unintended consequences.
    *   **Consider the order in which decorators are applied.** The order in which decorators are applied can affect their behavior. Make sure to apply them in the correct order.

*   **Project 6.1: Generic Retry Decorator**

    This project will create a generic retry decorator that can be applied to any function. The decorator will accept arguments for the number of retries and the delay between retries. The decorator will retry the function if it raises an exception, and it will log the exception and the retry attempt.

    ```python
    import time
    import functools
    import logging

    # Configure logging
    logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

    def retry(num_attempts, delay=1):
        """
        Generic retry decorator that retries a function if it raises an exception.

        Args:
            num_attempts: The number of times to retry the function.
            delay: The delay (in seconds) between retries.
        """
        def decorator(func):
            @functools.wraps(func)
            def wrapper(*args, **kwargs):
                for i in range(num_attempts):
                    try:
                        return func(*args, **kwargs)
                    except Exception as e:
                        logging.exception(f"Attempt {i + 1} failed: {e}")
                        time.sleep(delay)
                logging.error(f"Function {func.__name__} failed after {num_attempts} attempts")
                return None  # Or reraise the exception
            return wrapper
        return decorator


    @retry(num_attempts=3, delay=2)
    def flaky_function():
        """
        This function sometimes fails.
        """
        if random.random() < 0.5:
            raise ValueError("Function failed")
        return "Function succeeded"

    #Example Usage:
    if __name__ == '__main__':
        import random
        print(flaky_function())

    ```

    **Explanation:**

    1.  **`retry(num_attempts, delay=1)`:**  This is the parameterized decorator function.  It takes `num_attempts` and `delay` as arguments, allowing the user to customize the retry behavior.
    2.  **`decorator(func)`:** This is the decorator function that receives the function to be decorated (`func`).
    3.  **`wrapper(*args, **kwargs)`:** This is the wrapper function that executes the retry logic.
    4.  **`logging.exception(f"Attempt {i + 1} failed: {e}")`:** This logs the exception and the retry attempt using the `logging` module.  The `logging.exception` method automatically includes the traceback information, which is very helpful for debugging.
    5.  `logging.error(f"Function {func.__name__} failed after {num_attempts} attempts")`: Logs a final error if all attempts fail.

    This project demonstrates how to create a generic and reusable retry decorator that can be applied to any function. The decorator handles exception handling, logging, and retry logic, making it easy to add retry functionality to your code.

*   **6.8 Conclusion:**

    This chapter provided a comprehensive overview of decorators in Python. We explored what decorators are, how to create function and class decorators, and how to use them to add functionality to your code. We also looked at different use cases for decorators, such as logging, timing, authentication, and retry logic. By mastering decorators, you'll be well-equipped to write more reusable, maintainable, and Pythonic code.

    In the next chapter, we'll delve into context managers, exploring how to use them to manage resources effectively and ensure that your code is robust and reliable.

