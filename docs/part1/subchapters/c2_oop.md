---
title: Python Fundamentals
parent: Solidify Python Fundamentals
nav_order: 2
---
## Chapter 2: Object-Oriented Programming in Python

*   **2.1 Introduction to Object-Oriented Programming (OOP):**

    Object-Oriented Programming (OOP) is a programming paradigm centered around "objects" – self-contained entities that encapsulate data (attributes) and behavior (methods). Unlike procedural programming, which focuses on a sequence of instructions, OOP emphasizes modularity, reusability, and maintainability. The core principles of OOP are:

    *   **Encapsulation:** Bundling data and methods that operate on that data within a class, hiding internal implementation details and exposing a well-defined interface. This protects the data from accidental modification and simplifies code maintenance.
    *   **Inheritance:** Creating new classes (subclasses) based on existing classes (base classes), inheriting their attributes and methods. Inheritance promotes code reuse and allows you to create specialized versions of existing classes.
    *   **Polymorphism:** The ability of objects of different classes to respond to the same method call in their own way. This allows you to write code that can work with objects of different types without needing to know their specific class.
    *   **Abstraction:** Hiding complex implementation details and exposing only essential information to the user. Abstract classes and methods define a common interface for a set of related classes, without providing a concrete implementation.

    OOP allows us to better model real-world entities and their interactions, resulting in code that is easier to understand, modify, and extend. In the long run, it significantly reduces the complexity of software development, especially for large and intricate projects.

*   **2.2 Classes and Objects:**

    A class is a blueprint or template for creating objects. An object is an instance of a class. Let's explore how to define classes and create objects in Python.

    *   Classes are defined using the `class` keyword:

        ```python
        class Dog:
            pass # A minimal class definition
        ```

    *   The `__init__` method is a special method called the *constructor*. It is automatically called when a new object is created from the class. The `__init__` method is used to initialize the object's attributes. The first parameter of the `__init__` method is always `self`, which refers to the instance of the class being created.

        ```python
        class Dog:
            def __init__(self, name, breed):
                self.name = name
                self.breed = breed
        ```

        In this example, `name` and `breed` are instance attributes. Each `Dog` object will have its own unique `name` and `breed`.

    *   Methods are functions defined within a class. They operate on the object's attributes and can be called on instances of the class. The first parameter of a method is always `self`.

        ```python
        class Dog:
            def __init__(self, name, breed):
                self.name = name
                self.breed = breed

            def bark(self):
                print("Woof!")

            def wag_tail(self):
                print(f"{self.name} is wagging its tail.")

        my_dog = Dog("Buddy", "Golden Retriever")
        my_dog.bark()      # Output: Woof!
        my_dog.wag_tail()  # Output: Buddy is wagging its tail.
        ```

    *   Class attributes are attributes that are shared by all instances of a class. They are defined outside of the `__init__` method and are accessed using the class name (e.g., `Dog.species`). Class attributes are useful for storing data that is common to all instances of a class.

        ```python
        class Dog:
            species = "Canis familiaris" # Class attribute

            def __init__(self, name, breed):
                self.name = name
                self.breed = breed

        print(Dog.species) # Accessing the class attribute
        ```

*   **2.3 Inheritance:**

    Inheritance allows you to create new classes (subclasses or derived classes) based on existing classes (base classes or parent classes). The subclass inherits all the attributes and methods of the base class.

    *   To create a subclass, you specify the base class in parentheses after the subclass name:

        ```python
        class Animal:
            def __init__(self, name):
                self.name = name

            def speak(self):
                print("Generic animal sound")

        class Dog(Animal): # Dog inherits from Animal
            def speak(self):
                print("Woof!")

        my_dog = Dog("Buddy")
        my_dog.speak() # Output: Woof!
        ```

    *   The `super()` function is used to call methods from the parent class. This is useful when you want to extend the functionality of a parent class method without completely overriding it.

        ```python
        class Animal:
            def __init__(self, name, color="Unknown"):
                self.name = name
                self.color = color

            def speak(self):
                print("Generic animal sound")

        class Dog(Animal):
            def __init__(self, name, breed, color="Brown"):
                super().__init__(name, color) # Call the parent class's __init__
                self.breed = breed

            def speak(self):
                print("Woof!")

        my_dog = Dog("Buddy", "Golden Retriever")
        print(my_dog.color) # Output: Brown
        ```

    *   Method overriding allows you to change the behavior of inherited methods in the subclass. In the previous example, the `Dog` class overrides the `speak()` method of the `Animal` class.

    *   Multiple inheritance occurs when a class inherits from multiple base classes. While Python supports multiple inheritance, it can lead to complexities and potential issues, such as the "diamond problem" (where a class inherits from two classes that both inherit from a common ancestor, leading to ambiguity in which version of a method to inherit). It is generally recommended to use composition (where a class contains instances of other classes) instead of multiple inheritance whenever possible to avoid these issues.

*   **2.4 Polymorphism:**

    Polymorphism is the ability of objects of different classes to respond to the same method call in their own way. This allows you to write code that can work with objects of different types without needing to know their specific class.

    *   Duck typing is a form of polymorphism where the type of an object is less important than the methods it supports. If an object "walks like a duck and quacks like a duck," then it is treated as a duck, regardless of its actual class.

    *   Method overloading (having multiple methods with the same name but different parameters) is not directly supported in Python in the same way as in some other languages (like Java). However, you can achieve similar behavior using default argument values or the `@singledispatch` decorator from the `functools` module.  The `@singledispatch` decorator allows you to define multiple implementations of a function based on the type of its first argument.

        ```python
        from functools import singledispatch

        @singledispatch
        def describe(obj):
            print("Unknown object")

        @describe.register(int)
        def _(obj):
            print("Integer")

        @describe.register(str)
        def _(obj):
            print("String")

        describe(10)    # Output: Integer
        describe("hello") # Output: String
        describe([])    # Output: Unknown object
        ```

    *   Method overriding, as discussed in the Inheritance section, is a key part of polymorphism. It allows subclasses to provide their own implementations of methods inherited from their parent classes.

    *   Example:

        ```python
        class Dog:
            def speak(self):
                return "Woof!"

        class Cat:
            def speak(self):
                return "Meow!"

        def animal_sound(animal):
            return animal.speak() # Polymorphic call

        my_dog = Dog()
        my_cat = Cat()

        print(animal_sound(my_dog)) # Output: Woof!
        print(animal_sound(my_cat)) # Output: Meow!
        ```

        The `animal_sound` function can accept objects of any class that has a `speak` method. This is polymorphism in action.

*   **2.5 Encapsulation:**

    Encapsulation is the bundling of data and methods that operate on that data within a class, hiding internal implementation details and exposing a well-defined interface.

    *   Python uses naming conventions to indicate the visibility of attributes and methods:

        *   Public: Attributes and methods with no prefix are considered public and can be accessed from anywhere.
        *   Protected: Attributes and methods with a single underscore prefix (`_attribute`) are considered protected. They are intended to be accessed only within the class and its subclasses. However, this is just a convention; Python does not prevent you from accessing protected attributes from outside the class.
        *   Private: Attributes and methods with a double underscore prefix (`__attribute`) are considered private. Python uses name mangling to make it more difficult to access private attributes from outside the class.

    *   Name mangling: When you define an attribute with a double underscore prefix (e.g., `__balance`), Python renames the attribute to `_ClassName__attribute` (e.g., `_BankAccount__balance`). This makes it more difficult to access the attribute directly from outside the class, but it doesn't completely prevent it.

    *   Properties provide a way to control access to attributes using getter, setter, and deleter methods. This allows you to add validation logic or perform other actions when an attribute is accessed or modified.

        ```python
        class BankAccount:
            def __init__(self, account_number, initial_balance):
                self.account_number = account_number
                self.__balance = initial_balance # Private attribute

            @property
            def balance(self): # Getter
                return self.__balance

            @balance.setter
            def balance(self, amount): # Setter
                if amount < 0:
                    raise ValueError("Balance cannot be negative")
                self.__balance = amount

            @balance.deleter
            def balance(self): # Deleter
                print("Deleting balance is not allowed")

        my_account = BankAccount("1234567890", 1000)
        print(my_account.balance) # Output: 1000
        my_account.balance = 1500
        print(my_account.balance) # Output: 1500

        #my_account.balance = -500 # Raises ValueError
        del my_account.balance
        ```

*   **2.6 Abstraction:**

    Abstraction is the process of hiding complex implementation details and exposing only essential information to the user. Abstract base classes (ABCs) provide a way to define abstract methods, which are methods that must be implemented by subclasses.

    *   To define an abstract base class, you use the `abc` module:

        ```python
        from abc import ABC, abstractmethod

        class Shape(ABC):
            @abstractmethod
            def area(self):
                pass

            @abstractmethod
            def perimeter(self):
                pass
        ```

        The `ABC` class is the base class for abstract base classes. The `@abstractmethod` decorator is used to define abstract methods.

        A class that inherits from an ABC must implement all of the abstract methods defined in the ABC. If it doesn't, the class will also be considered an abstract class and cannot be instantiated.

        ```python
        class Circle(Shape):
            def __init__(self, radius):
                self.radius = radius

            def area(self):
                return 3.14 * self.radius**2

            def perimeter(self):
                return 2 * 3.14 * self.radius

        #invalid case
        #class Rectangle(Shape):
        #    def __init__(self, length, width):
        #        self.length = length
        #        self.width = width

        #my_shape = Shape() ## invalid case, abstract classes cannnot be instantiated
        my_circle = Circle(5)
        print(my_circle.area())

        #my_rectangle = Rectangle(10, 20) # will raise error!
        ```

*   **2.7 Special Methods (Magic Methods):**

    Special methods (also known as magic methods or dunder methods) are methods with double underscores before and after their names (e.g., `__init__`, `__str__`, `__add__`). These methods are used to implement special behavior for your classes, such as string representation, length calculation, indexing, and operator overloading.

    *   `__str__` and `__repr__`: The `__str__` method is used to return a human-readable string representation of an object. The `__repr__` method is used to return an unambiguous string representation of an object (often used for debugging). If `__str__` is not defined, Python will use `__repr__` instead.

        ```python
        class Point:
            def __init__(self, x, y):
                self.x = x
                self.y = y

            def __str__(self):
                return f"Point({self.x}, {self.y})"

            def __repr__(self):
                return f"Point(x={self.x}, y={self.y})"

        p = Point(10, 20)
        print(str(p)) # Output: Point(10, 20)
        print(repr(p)) # Output: Point(x=10, y=20)
        ```

    *   `__len__`: The `__len__` method is used to return the length of an object.

        ```python
        class MyList:
            def __init__(self, data):
                self.data = data

            def __len__(self):
                return len(self.data)

        my_list = MyList([1, 2, 3, 4, 5])
        print(len(my_list)) # Output: 5
        ```

    *   `__getitem__` and `__setitem__`: The `__getitem__` method is used to implement indexing (e.g., `my_object[index]`). The `__setitem__` method is used to implement assignment to an index (e.g., `my_object[index] = value`).

        ```python
        class MyList:
            def __init__(self, data):
                self.data = data

            def __getitem__(self, index):
                return self.data[index]

            def __setitem__(self, index, value):
                self.data[index] = value

        my_list = MyList([1, 2, 3, 4, 5])
        print(my_list[2]) # Output: 3
        my_list[2] = 10
        print(my_list[2]) # Output: 10
        ```

    *   `__add__`, `__sub__`, etc.: These methods are used to overload operators such as `+`, `-`, `*`, `/`, etc. This allows you to define how these operators should behave when applied to objects of your class.

        ```python
        class Vector:
            def __init__(self, x, y):
                self.x = x
                self.y = y

            def __add__(self, other):
                return Vector(self.x + other.x, self.y + other.y)

            def __str__(self):
                return f"Vector({self.x}, {self.y})"

        v1 = Vector(1, 2)
        v2 = Vector(3, 4)
        v3 = v1 + v2 # Operator overloading
        print(v3) # Output: Vector(4, 6)
        ```

*   **2.8 Design Patterns (Brief Introduction):**

    Design patterns are reusable solutions to commonly occurring problems in software design. They are not specific pieces of code, but rather general templates or blueprints that can be adapted to solve a variety of problems.

    *   Singleton: Ensures that a class has only one instance and provides a global point of access to it. We'll explore a more advanced implementation of the Singleton pattern using metaclasses in a later chapter.

    *   Factory: Provides an interface for creating objects without specifying their concrete classes. This allows you to decouple the client code from the concrete classes being created, making your code more flexible and maintainable.

    *   Observer: Defines a one-to-many dependency between objects, so that when one object changes state, all its dependents are notified and updated automatically.

*   **2.9 Conclusion:**

    This chapter provided a comprehensive overview of object-oriented programming in Python. We covered classes, objects, inheritance, polymorphism, encapsulation, abstraction, special methods, and design patterns. By mastering these concepts, you'll be well-equipped to design and implement complex, maintainable, and reusable software systems.

    In the next chapter, we'll delve into metaclasses and dynamic class creation, exploring how to create classes programmatically and customize the class creation process.

**Project 2.1: A Simulation of a Simple Ecosystem - Advanced OOP Implementation**

*   **Description:** Create a simulation of a small ecosystem with different types of organisms (e.g., Plants, Herbivores, Carnivores). Use OOP principles to model the organisms, their behavior (e.g., eating, moving, reproducing), and their interactions. This project will *require* you to use key OOP principles such as:
    *   **Abstraction:** Use abstract base classes to define the common interface for all organisms (`Organism` ABC with abstract methods like `eat()`, `move()`, `reproduce()`).
    *   **Inheritance:** Create concrete classes for `Plant`, `Herbivore`, and `Carnivore` that inherit from the `Organism` ABC and implement the abstract methods.
    *   **Polymorphism:** Implement a `simulate_interaction()` function that can handle interactions between different types of organisms (e.g., a `Carnivore` eating an `Herbivore`, an `Herbivore` eating a `Plant`). This will involve calling the appropriate methods on the objects based on their type.
    *   **Encapsulation:** Encapsulate the internal state of each organism (e.g., energy level, health) using private attributes and provide controlled access to these attributes using properties.

*   **Core Requirements:**
    1.  **Abstract Base Class (`Organism`):**
        *   Define an `Organism` ABC with abstract methods:
            *   `eat(self, food)`: Defines how the organism consumes food.
            *   `move(self)`: Defines how the organism moves within the ecosystem.
            *   `reproduce(self)`: Defines how the organism reproduces.
            *   `is_alive(self)`: Returns `True` if the organism is alive, `False` otherwise.
        *   Include an abstract attribute, `energy`, which is a property with a getter and setter.
    2.  **Concrete Classes (`Plant`, `Herbivore`, `Carnivore`):**
        *   Implement the `Plant`, `Herbivore`, and `Carnivore` classes, inheriting from `Organism`.
        *   Implement the abstract methods in each class, defining specific behaviors for each type of organism.
            *   `Plant`:
                *   `eat()`: Plants gain energy through photosynthesis (no `food` argument).
                *   `move()`: Plants don't move.
                *   `reproduce()`: Plants reproduce by spreading seeds.
            *   `Herbivore`:
                *   `eat(food)`: Herbivores can only eat `Plant` objects.
                *   `move()`: Herbivores move to find plants.
                *   `reproduce()`: Herbivores reproduce when they have enough energy.
            *   `Carnivore`:
                *   `eat(food)`: Carnivores can only eat `Herbivore` objects.
                *   `move()`: Carnivores move to find herbivores.
                *   `reproduce()`: Carnivores reproduce when they have enough energy.
        *   Include specific attributes for each class (e.g., `Plant` might have a `growth_rate`, `Herbivore` might have a `speed`, `Carnivore` might have a `attack_power`).
    3.  **Ecosystem Class:**
        *   Create an `Ecosystem` class to manage the organisms.
        *   The `Ecosystem` class should have methods for:
            *   `add_organism(organism)`: Adds an organism to the ecosystem.
            *   `remove_organism(organism)`: Removes an organism from the ecosystem.
            *   `simulate_step()`: Simulates a single step in the ecosystem.
            *   `display()`: Prints the current state of the ecosystem (e.g., the number of organisms of each type, their energy levels).
    4.  **Simulation Logic:**
        *   Implement the `simulate_step()` method in the `Ecosystem` class to simulate the interactions between organisms.
        *   The `simulate_step()` method should:
            *   Iterate over all organisms in the ecosystem.
            *   For each organism, call its `move()` method.
            *   If the organism is hungry, call its `eat()` method, passing in a suitable food source (if available).
            *   If the organism has enough energy, call its `reproduce()` method.
            *   Remove any dead organisms from the ecosystem (using the `is_alive()` method).
    5.  **Properties and Encapsulation:**
        *   Use properties to control access to the `energy` attribute of each organism.
        *   The `energy` property should:
            *   Raise a `ValueError` if the energy is set to a negative value.
            *   Automatically set `is_alive` to `False` if the energy reaches zero.

*   **Enhancements (for more advanced learners):**
    *   Use abstract base classes to define interfaces for different types of organisms.
    *   Implement a simple event system to simulate events such as births, deaths, and interactions between organisms.
    *   Add a graphical user interface (GUI) using Tkinter or PyQt to visualize the simulation.

This project will allow you to practice advanced OOP implementations and it is the best approach for the book to show advanced python implementations.



```python
from abc import ABC, abstractmethod
import random

class Organism(ABC):
    """
    Abstract base class for all organisms in the ecosystem.
    """

    def __init__(self, name, initial_energy):
        self.name = name
        self._energy = initial_energy  # Private attribute for energy
        self.is_alive = True

    @property
    def energy(self):
        """
        Property for accessing the organism's energy level.
        """
        return self._energy

    @energy.setter
    def energy(self, value):
        """
        Setter for the organism's energy level.
        """
        if value < 0:
            raise ValueError("Energy cannot be negative.")
        self._energy = value
        if self._energy <= 0:
            self.is_alive = False

    @abstractmethod
    def eat(self, food):
        """
        Abstract method for eating.
        """
        pass

    @abstractmethod
    def move(self):
        """
        Abstract method for moving.
        """
        pass

    @abstractmethod
    def reproduce(self):
        """
        Abstract method for reproducing.
        """
        pass

    @abstractmethod
    def __str__(self):
        """
        Abstract method for string representation.
        """
        pass

# -----------------------------------------------------------------------------
# Concrete Classes
# -----------------------------------------------------------------------------

class Plant(Organism):
    """
    Represents a plant in the ecosystem.
    """

    def __init__(self, name, initial_energy=50, growth_rate=10):
        super().__init__(name, initial_energy)
        self.growth_rate = growth_rate

    def eat(self, food=None):
        """
        Plants gain energy through photosynthesis (no food needed).
        """
        self.energy += self.growth_rate
        print(f"{self.name} photosynthesizes and gains {self.growth_rate} energy.")

    def move(self):
        """
        Plants don't move.
        """
        print(f"{self.name} stays put.")

    def reproduce(self):
        """
        Plants reproduce by spreading seeds.
        """
        if self.energy >= 100:
            self.energy -= 50
            print(f"{self.name} spreads seeds and reproduces.")
            return Plant(f"{self.name}'s offspring", initial_energy=25)  # Returns new plant
        return None  # No reproduction

    def __str__(self):
        return f"Plant(name='{self.name}', energy={self.energy})"


class Herbivore(Organism):
    """
    Represents a herbivore in the ecosystem.
    """

    def __init__(self, name, initial_energy=75, speed=5):
        super().__init__(name, initial_energy)
        self.speed = speed

    def eat(self, food):
        """
        Herbivores can only eat Plant objects.
        """
        if isinstance(food, Plant):
            energy_gain = food.energy // 2  # Herbivore gets half the plant's energy
            self.energy += energy_gain
            food.energy -= energy_gain
            print(f"{self.name} eats {food.name} and gains {energy_gain} energy.")
        else:
            print(f"{self.name} tries to eat {food.name}, but it's not a plant!")

    def move(self):
        """
        Herbivores move to find plants.
        """
        print(f"{self.name} moves around in search of plants.")
        self.energy -= self.speed # Moving costs energy

    def reproduce(self):
        """
        Herbivores reproduce when they have enough energy.
        """
        if self.energy >= 150:
            self.energy -= 75
            print(f"{self.name} reproduces.")
            return Herbivore(f"{self.name}'s offspring", initial_energy=50)  # Returns new herbivore
        return None  # No reproduction

    def __str__(self):
        return f"Herbivore(name='{self.name}', energy={self.energy})"


class Carnivore(Organism):
    """
    Represents a carnivore in the ecosystem.
    """

    def __init__(self, name, initial_energy=100, attack_power=15):
        super().__init__(name, initial_energy)
        self.attack_power = attack_power

    def eat(self, food):
        """
        Carnivores can only eat Herbivore objects.
        """
        if isinstance(food, Herbivore):
            energy_gain = food.energy
            self.energy += energy_gain
            food.energy = 0  # Herbivore is eaten
            food.is_alive = False
            print(f"{self.name} eats {food.name} and gains {energy_gain} energy.")
        else:
            print(f"{self.name} tries to eat {food.name}, but it's not a herbivore!")

    def move(self):
        """
        Carnivores move to find herbivores.
        """
        print(f"{self.name} stalks for herbivores.")
        self.energy -= self.attack_power // 3 # Stalking costs energy

    def reproduce(self):
        """
        Carnivores reproduce when they have enough energy.
        """
        if self.energy >= 200:
            self.energy -= 100
            print(f"{self.name} reproduces.")
            return Carnivore(f"{self.name}'s offspring", initial_energy=75)  # Returns new carnivore
        return None  # No reproduction

    def __str__(self):
        return f"Carnivore(name='{self.name}', energy={self.energy})"


# -----------------------------------------------------------------------------
# Ecosystem Class
# -----------------------------------------------------------------------------

class Ecosystem:
    """
    Manages the organisms and simulates their interactions.
    """

    def __init__(self):
        self.organisms = []

    def add_organism(self, organism):
        """
        Adds an organism to the ecosystem.
        """
        self.organisms.append(organism)

    def remove_organism(self, organism):
        """
        Removes an organism from the ecosystem.
        """
        if organism in self.organisms:
            self.organisms.remove(organism)

    def simulate_step(self):
        """
        Simulates a single step in the ecosystem.
        """
        print("\n--- Simulating a Step ---")
        for organism in list(self.organisms):  # Iterate over a *copy* of the list
            if not organism.is_alive:
                self.remove_organism(organism)
                continue

            organism.move()

            # Eating
            if isinstance(organism, Herbivore):
                # Find a plant to eat
                available_plants = [o for o in self.organisms if isinstance(o, Plant) and o.is_alive]
                if available_plants:
                    plant_to_eat = random.choice(available_plants)
                    organism.eat(plant_to_eat)
            elif isinstance(organism, Carnivore):
                # Find a herbivore to eat
                available_herbivores = [o for o in self.organisms if isinstance(o, Herbivore) and o.is_alive]
                if available_herbivores:
                    herbivore_to_eat = random.choice(available_herbivores)
                    organism.eat(herbivore_to_eat)
            elif isinstance(organism, Plant):
                organism.eat()  # Plants photosynthesize

            # Reproducing
            new_organism = organism.reproduce()
            if new_organism:
                self.add_organism(new_organism)

            if not organism.is_alive: # Check if died during eat
                self.remove_organism(organism)

    def display(self):
        """
        Prints the current state of the ecosystem.
        """
        print("\n--- Ecosystem State ---")
        plant_count = sum(1 for o in self.organisms if isinstance(o, Plant))
        herbivore_count = sum(1 for o in self.organisms if isinstance(o, Herbivore))
        carnivore_count = sum(1 for o in self.organisms if isinstance(o, Carnivore))

        print(f"Plants: {plant_count}")
        print(f"Herbivores: {herbivore_count}")
        print(f"Carnivores: {carnivore_count}")

        for organism in self.organisms:
            print(f"  - {organism}")


# -----------------------------------------------------------------------------
# Example Usage
# -----------------------------------------------------------------------------

if __name__ == "__main__":
    # Create an ecosystem
    ecosystem = Ecosystem()

    # Add some initial organisms
    ecosystem.add_organism(Plant("Oak", initial_energy=70))
    ecosystem.add_organism(Plant("Grass", initial_energy=40))
    ecosystem.add_organism(Herbivore("Rabbit", initial_energy=80))
    ecosystem.add_organism(Carnivore("Fox", initial_energy=120))

    # Run the simulation for a few steps
    for i in range(5):
        print(f"\n--- Step {i + 1} ---")
        ecosystem.simulate_step()
        ecosystem.display()
```

**Key Improvements and Explanations:**

1.  **`Ecosystem` Class:**
    *   `__init__`: Initializes the `organisms` list to hold all organisms in the simulation.
    *   `add_organism`, `remove_organism`: Methods to manage the organism population.
    *   `simulate_step`: This is the *heart* of the simulation.  It iterates through the organisms, making them move, eat, and reproduce. Critically, it iterates over a *copy* of the `self.organisms` list (`list(self.organisms)`) because the list can be modified during the iteration (e.g., when an organism reproduces or dies). It also checks `is_alive` to avoid acting on dead organisms.
    *   `display`: Prints a summary of the ecosystem's state, including the number of each type of organism and details about each individual.
2.  **Simulation Logic:**
    *   The `simulate_step` method now includes logic for:
        *   **Moving:** Each organism moves (consuming energy).
        *   **Eating:** Herbivores find and eat plants, Carnivores find and eat herbivores.  Plants "eat" by photosynthesizing.
        *   **Reproducing:** If an organism has enough energy, it reproduces (creating a new organism and reducing its own energy).
        *   **Death:** If an organism's energy drops to zero, it dies and is removed from the ecosystem.
3.  **`if __name__ == "__main__":` Block:**
    *   This ensures that the simulation code is only executed when the script is run directly (not when it's imported as a module).  It creates an `Ecosystem`, adds some initial organisms, and runs the simulation for a few steps.

**To Run the Code:**

1.  Save the code as a Python file (e.g., `ecosystem.py`).
2.  Run it from your terminal: `python ecosystem.py`

