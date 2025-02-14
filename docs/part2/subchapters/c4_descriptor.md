---
title: Descriptors and Attribute Management
parent: Advanced Python Concepts
nav_order: 3
---

## Chapter 4: Descriptors and Attribute Management

*   **4.1 Introduction to Descriptors:**

    Descriptors are a powerful and often underutilized feature in Python that allows you to exert fine-grained control over attribute access in your classes. They provide a way to customize what happens when an attribute is accessed (read), set (written), or deleted. In essence, descriptors are objects that define how attributes are managed.

    Why use descriptors? They enable you to:

    *   **Validate Attribute Values:** Ensure that attribute values meet certain criteria (e.g., type, range).
    *   **Implement Lazy Loading:** Load attribute values only when they are first accessed, improving performance.
    *   **Create Read-Only Attributes:** Prevent modification of certain attributes.
    *   **Create Computed Attributes:** Dynamically compute attribute values based on other attributes.
    *   **Type checking:** Make sure that the value has the correct type

    The key to understanding descriptors lies in the *descriptor protocol*. This protocol consists of three special methods:

    *   `__get__(self, instance, owner)`: Called when the attribute is accessed (read). It returns the attribute value.
    *   `__set__(self, instance, value)`: Called when the attribute is set (written). It modifies the attribute value.
    *   `__delete__(self, instance)`: Called when the attribute is deleted using the `del` statement.

    Not all descriptors implement all three methods. Descriptors are categorized as either *data descriptors* or *non-data descriptors* based on which methods they implement:

    *   **Data Descriptors:** Implement both `__get__` and `__set__`. They have the most control over attribute access.
    *   **Non-Data Descriptors:** Implement only `__get__`. They are typically used for read-only attributes or computed attributes. Functions and methods are implemented using non-data descriptors.

*   **4.2 Implementing Descriptors:**

    To create a descriptor, you define a class that implements one or more of the descriptor protocol methods (`__get__`, `__set__`, `__delete__`).

    Let's start with a simple example: a descriptor that validates that an attribute value is an integer.

    ```python
    class Integer:
        def __set_name__(self, owner, name):
            self.name = name

        def __get__(self, instance, owner):
            if instance is None:
                return self
            return instance.__dict__[self.name]

        def __set__(self, instance, value):
            if not isinstance(value, int):
                raise TypeError(f"{self.name} must be an integer")
            instance.__dict__[self.name] = value
    ```

    **Explanation:**

    1.  **`Integer` Class:** This class implements the descriptor protocol.
    2.  **`__set_name__` method:** It is called at the time of class creation to bind name to the descriptor.
    3.  **`__get__` Method:** This method is called when the attribute is accessed. It retrieves the attribute value from the instance's `__dict__`. If `instance` is `None`, it means the attribute is being accessed on the class itself (e.g., `MyClass.my_attribute`), so we return the descriptor object itself.
    4.  **`__set__` Method:** This method is called when the attribute is set. It first validates that the value is an integer. If it's not, it raises a `TypeError`. If it is, it stores the value in the instance's `__dict__`.

    To use this descriptor, you declare it as a class attribute:

    ```python
    class MyClass:
        my_attribute = Integer()

        def __init__(self, my_attribute):
            self.my_attribute = my_attribute # The __set__ method of the descriptor is called here
    ```

    Now, when you create an instance of `MyClass` and try to set `my_attribute` to a non-integer value, a `TypeError` will be raised:

    ```python
    obj = MyClass(10)
    print(obj.my_attribute)
    obj.my_attribute = 20 # Works fine
    #obj.my_attribute = "hello" # Raises TypeError
    ```

*   **4.3 Descriptor Use Cases:**

    Let's explore some common use cases for descriptors in more detail.

    *   **Validation:** As demonstrated in the previous example, descriptors can be used to enforce validation rules. You can check the type of the value, its range, or any other criteria.

        ```python
        class PositiveNumber:
            def __set_name__(self, owner, name):
                self.name = name

            def __get__(self, instance, owner):
                if instance is None:
                    return self

                return instance.__dict__[self.name]

            def __set__(self, instance, value):
                if not isinstance(value, (int, float)):
                    raise TypeError(f"{self.name} must be a number")
                if value <= 0:
                    raise ValueError(f"{self.name} must be positive")
                instance.__dict__[self.name] = value
        ```

    *   **Read-Only Attributes:** You can create read-only attributes by defining a descriptor that implements only the `__get__` method and raises an `AttributeError` in the `__set__` method.

        ```python
        class ReadOnly:
            def __set_name__(self, owner, name):
                self.name = name

            def __get__(self, instance, owner):
                if instance is None:
                    return self
                return instance.__dict__[self.name]

            def __set__(self, instance, value):
                raise AttributeError("Cannot set read-only attribute")

        class MyClass:
            read_only_attribute = ReadOnly()

            def __init__(self, value):
                self.read_only_attribute = value

        obj = MyClass(10)
        print(obj.read_only_attribute)
        #obj.read_only_attribute = 20 # Raises AttributeError
        ```

    *   **Computed Attributes:** You can create attributes whose values are computed dynamically based on other attributes.

        ```python
        class Circle:
            radius = PositiveNumber()

            def __init__(self, radius):
                self.radius = radius

            @property
            def area(self):
                return 3.14 * self.radius**2
        ```

        In this example, `area` is a computed attribute that is calculated based on the `radius`. The `@property` decorator (explained later) provides a convenient way to create computed attributes.

    *   **Lazy Loading:** Lazy loading defers the loading of an attribute's value until it is first accessed. This can improve performance, especially for attributes that are expensive to compute or load from a database.

        ```python
        import time

        class LazyLoad:
            def __init__(self, func):
                self.func = func
                self.name = None

            def __set_name__(self, owner, name):
                self.name = name

            def __get__(self, instance, owner):
                if instance is None:
                    return self
                if self.name not in instance.__dict__:
                    print(f"Loading {self.name}...")
                    start = time.time()
                    value = self.func(instance)  # Load the value
                    end = time.time()
                    print(f"Loading complete in {end-start} seconds")
                    instance.__dict__[self.name] = value  # Cache the value
                return instance.__dict__[self.name]


        class MyClass:
            def __init__(self, data_source):
                self.data_source = data_source

            @LazyLoad
            def expensive_attribute(self):
                # Simulate an expensive operation (e.g., reading from a database)
                print("Performing expensive calculation...")
                time.sleep(2) # Simulate a delay
                return self.data_source + " (processed)"

        obj = MyClass("some data")
        print("First access:", obj.expensive_attribute) # The expensive calculation is performed
        print("Second access:", obj.expensive_attribute) # The cached value is returned immediately
        ```

*   **4.4 Non-Data Descriptors (Functions and Methods):**

    Functions and methods in Python are implemented using non-data descriptors. When you access a method on a class (e.g., `MyClass.my_method`), you get the unbound method. When you access a method on an instance of a class (e.g., `obj.my_method`), you get the bound method.

    *   **Unbound Method:** An unbound method is a function that is not bound to a specific instance of the class. You must explicitly pass the instance as the first argument when calling an unbound method.

    *   **Bound Method:** A bound method is a function that is bound to a specific instance of the class. The instance is automatically passed as the first argument (the `self` argument) when calling a bound method.

    ```python
    class MyClass:
        def my_method(self):
            print("Hello from my_method!")

    obj = MyClass()

    # Unbound method
    unbound_method = MyClass.my_method
    unbound_method(obj) # Output: Hello from my_method!

    # Bound method
    bound_method = obj.my_method
    bound_method() # Output: Hello from my_method!
    ```

    When you access `obj.my_method`, Python's descriptor mechanism kicks in. The `__get__` method of the function descriptor returns a bound method object that automatically passes `obj` as the `self` argument when the method is called. This is how Python magic happens.

*   **4.5 `@property` Decorator:**

    The `@property` decorator provides a convenient way to create simple descriptors for managing attribute access. It allows you to define getter, setter, and deleter methods for an attribute without having to create a separate descriptor class.

    ```python
    class MyClass:
        def __init__(self, value):
            self._my_attribute = value

        @property
        def my_attribute(self):
            """Getter for my_attribute."""
            return self._my_attribute

        @my_attribute.setter
        def my_attribute(self, value):
            """Setter for my_attribute."""
            if value < 0:
                raise ValueError("Value must be non-negative")
            self._my_attribute = value

        @my_attribute.deleter
        def my_attribute(self):
            """Deleter for my_attribute."""
            del self._my_attribute
    ```

    **Explanation:**

    *   `@property`: This decorator transforms the `my_attribute` method into a getter. When you access `obj.my_attribute`, the `my_attribute` method is called, and its return value is returned as the attribute value.
    *   `@my_attribute.setter`: This decorator transforms the `my_attribute` method into a setter. When you assign a value to `obj.my_attribute`, the `my_attribute` method is called with the new value as an argument.
    *   `@my_attribute.deleter`: This decorator transforms the `my_attribute` method into a deleter. When you use the `del obj.my_attribute` statement, the `my_attribute` method is called.

    **Advantages of `@property`:**

    *   More concise syntax compared to implementing descriptors directly.
    *   Easier to read and understand for simple cases.

    **Disadvantages of `@property`:**

    *   Less flexible than implementing descriptors directly.
    *   Cannot be used for lazy loading or other advanced attribute management techniques.
    *   It is implemented using descriptors, so you still need to understand the descriptor protocol to use `@property` effectively.

*   **4.6 Descriptor Best Practices:**

    *   **Keep descriptors simple and focused.** Descriptors should perform a single, well-defined task. Avoid complex logic or side effects.
    *   **Document your descriptors clearly.** Explain the purpose of the descriptor, how it works, and any assumptions it makes.
    *   **Avoid side effects in `__get__` (especially for non-data descriptors).** The `__get__` method should not modify the state of the object or perform any other actions that could have unintended consequences.
    *   **Consider using `@property` for simple cases.** If you only need to implement basic getter, setter, and deleter methods, `@property` is often the best choice.

*   **Project 4.1: Unit Conversion System**

    This project will demonstrate how to use descriptors to create a unit conversion system. The system will allow you to define different units (e.g., meters, feet, inches) and convert values between them. Descriptors will be used to validate the input values and perform the unit conversions automatically.

    ```python
    class Unit:
        """
        Descriptor for handling unit conversions.
        """
        def __init__(self, unit_type, conversion_factor):
            self.unit_type = unit_type # e.g., 'length', 'temperature'
            self.conversion_factor = conversion_factor # Conversion to base unit

        def __set_name__(self, owner, name):
            self.name = name

        def __get__(self, instance, owner):
            if instance is None:
                return self
            return instance.__dict__[self.name]

        def __set__(self, instance, value):
            if not isinstance(value, (int, float)):
                raise TypeError(f"{self.name} must be a number")
            instance.__dict__[self.name] = value

        def to_base_unit(self, value):
            """Converts the value to the base unit."""
            return value * self.conversion_factor

        def from_base_unit(self, value):
            """Converts the value from the base unit."""
            return value / self.conversion_factor


    class Length:
        """
        Class for representing lengths in different units.
        """
        meters = Unit('length', 1.0) # Base unit
        feet = Unit('length', 0.3048)
        inches = Unit('length', 0.0254)

        def __init__(self, meters=0, feet=0, inches=0):
            self.meters = meters
            self.feet = feet
            self.inches = inches

        def to_meters(self):
            """Converts the length to meters."""
            total_meters = (
                self.meters +
                Length.feet.to_base_unit(self.feet) +
                Length.inches.to_base_unit(self.inches)
            )
            return total_meters

    #Example Usage
    if __name__ == '__main__':
        length = Length(meters=1, feet=2, inches=6) # 1 meter, 2 feet, 6 inches
        total_meters = length.to_meters()
        print(f"Total length in meters: {total_meters}")

        length.feet = 10 # Set the length to 10 feet
        print(f"Total length in meters after setting feet: {length.to_meters()}")

        #length.meters = "hello" # Raises TypeError
    ```

    **Explanation:**

    1.  **`Unit` Descriptor:** The `Unit` descriptor handles the unit conversion logic. It stores the unit type (e.g., 'length', 'temperature') and the conversion factor to the base unit (e.g., meters for length).
    2.  **`Length` Class:** The `Length` class represents lengths in different units. It defines `meters`, `feet`, and `inches` as `Unit` descriptors.
    3.  **`to_meters` Method:** This method converts the length to meters by using the `to_base_unit` method of the `Unit` descriptor.
    4.  **Example Usage:** The example code creates a `Length` object and converts it to meters. It also demonstrates how to set the length in feet and how the `Unit` descriptor enforces type validation.

    This project demonstrates how descriptors can be used to create a flexible and robust unit conversion system. By encapsulating the unit conversion logic in a descriptor, you can ensure that the unit conversions are performed correctly and consistently.

*   **4.7 Conclusion:**

    This chapter provided a comprehensive overview of descriptors and attribute management in Python. We explored what descriptors are, how they work, and how they can be used to control attribute access and modification. We also looked at different use cases for descriptors, such as validation, read-only attributes, computed attributes, and lazy loading. Finally, we explored the `@property` decorator and how it can be used to create simple descriptors. By mastering descriptors, you'll be well-equipped to design and implement more robust and maintainable Python classes.

    In the next chapter, we'll delve into generators and coroutines, exploring how to write memory-efficient and asynchronous code in Python.

