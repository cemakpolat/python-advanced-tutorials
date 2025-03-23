---
title: Testing and Debugging Strategies
parent: Applying Advanced Concepts 
nav_order: 4
---

## Chapter 13: Testing and Debugging Strategies

*   **13.1 Introduction to Testing:**

    Testing is an indispensable part of the software development lifecycle. It's the process of verifying and validating that a piece of software meets its requirements and functions as intended. Effective testing helps to ensure code correctness, prevent regressions (reintroduction of bugs), and improve the overall maintainability of the codebase.

    Why is testing important?

    *   **Ensuring Code Correctness:** Tests verify that the code behaves as expected under various conditions and inputs.
    *   **Preventing Regressions:** Tests help to catch regressions, ensuring that new changes don't break existing functionality.
    *   **Improving Maintainability:** Well-tested code is easier to refactor and maintain because you can be confident that changes won't introduce unexpected side effects.
    *   **Enabling Collaboration:** Good tests serve as documentation of your code, showing what the code should do.

    **Types of Testing:**

    *   **Unit Testing:** Testing individual units of code, such as functions, classes, or modules, in isolation. The goal of unit testing is to verify that each unit of code works correctly on its own.
    *   **Integration Testing:** Testing the interactions between different units of code or between different systems. The goal of integration testing is to verify that the different parts of the system work together correctly.
    *   **System Testing:** Testing the entire system as a whole. The goal of system testing is to verify that the system meets its overall requirements.
    *   **Acceptance Testing:** Testing the system from the perspective of the end-user. The goal of acceptance testing is to verify that the system meets the user's needs and expectations.

    **Test-Driven Development (TDD):**

    Test-Driven Development (TDD) is a software development process in which you write tests *before* writing the code that implements the functionality. The TDD cycle typically involves the following steps:

    1.  **Write a test:** Write a test that defines the desired behavior of the code.
    2.  **Run the test:** The test should fail because the code hasn't been written yet.
    3.  **Write the code:** Write the minimum amount of code necessary to make the test pass.
    4.  **Run the test:** The test should now pass.
    5.  **Refactor:** Refactor the code to improve its design and maintainability.

    TDD can help you to write more focused, testable, and maintainable code.

    **The Testing Pyramid:**

    The testing pyramid is a model that suggests the ideal proportions of different types of tests in a software project. The pyramid has three layers:

    *   **Unit Tests:** The base of the pyramid represents unit tests. There should be a large number of unit tests because they are fast, easy to write, and provide detailed feedback on the correctness of individual units of code.
    *   **Integration Tests:** The middle layer represents integration tests. There should be fewer integration tests than unit tests because they are more complex and take longer to run.
    *   **End-to-End Tests:** The top of the pyramid represents end-to-end tests (also known as system tests or acceptance tests). There should be the fewest end-to-end tests because they are the most complex, take the longest to run, and are the most brittle.

*   **13.2 Unit Testing with `pytest`:**

    `pytest` is a popular and powerful testing framework for Python. It provides a simple and flexible way to write and run tests, and it supports a wide range of features, such as fixtures, parametrization, and plugins.

    **Installing and Configuring `pytest`:**

    You can install `pytest` using pip:

    ```bash
    pip install pytest
    ```

    **Writing Basic Unit Tests with `pytest`:**

    To write a unit test with `pytest`, you simply define a function that starts with the prefix `test_`. The test function should contain assertions that verify the expected behavior of the code being tested.

    ```python
    # my_module.py

    def add(x, y):
        return x + y
    ```

    ```python
    # test_my_module.py

    from my_module import add

    def test_add():
        assert add(2, 3) == 5
        assert add(-1, 1) == 0
        assert add(0, 0) == 0
    ```

    **Test Discovery and Naming Conventions:**

    `pytest` automatically discovers tests by looking for files that start with `test_` or end with `_test`, and for functions and methods that start with `test_`.

    **Fixtures:**

    Fixtures are functions that provide test data or setup/teardown logic for tests. You can define fixtures using the `@pytest.fixture` decorator.

    ```python
    import pytest

    @pytest.fixture
    def my_fixture():
        """A simple fixture that returns a list of numbers."""
        return [1, 2, 3]

    def test_my_fixture(my_fixture):
        """A test that uses the my_fixture fixture."""
        assert len(my_fixture) == 3
        assert my_fixture[0] == 1
    ```

    **Parametrization:**

    Parametrization allows you to run the same test with different inputs. You can use the `@pytest.mark.parametrize` decorator to parametrize a test function.

    ```python
    import pytest

    @pytest.mark.parametrize("input1, input2, expected", [
        (2, 3, 5),
        (-1, 1, 0),
        (0, 0, 0),
    ])
    def test_add_parametrize(input1, input2, expected):
        """A parametrized test for the add function."""
        from my_module import add
        assert add(input1, input2) == expected
    ```

    **Markers:**

    Markers are used to categorize and select tests. You can define markers using the `@pytest.mark` decorator.

    ```python
    import pytest

    @pytest.mark.slow
    def test_slow_function():
        """A slow test."""
        assert True

    def test_fast_function():
        """A fast test."""
        assert True
    ```

    You can then use the `-m` option to select tests based on their markers:

    ```bash
    pytest -m slow # Only run tests with the "slow" marker
    pytest -m "not slow" # Run tests that do NOT have the "slow" marker
    ```

    **Plugins:**

    `pytest` has a rich plugin ecosystem that allows you to extend its functionality. Some popular plugins include:

    *   `pytest-cov`: For measuring code coverage.
    *   `pytest-mock`: For mocking objects and functions.
    *   `pytest-django`: For testing Django applications.

*   **13.3 Mocking with `unittest.mock`:**

    Mocking is a technique for replacing real objects with mock objects for testing. Mock objects are objects that simulate the behavior of real objects, but they are simpler and easier to control.

    Why use mocking?

    *   **Isolating Units of Code:** Mocking allows you to test a unit of code in isolation, without relying on external dependencies.
    *   **Simulating Dependencies:** Mocking allows you to simulate the behavior of dependencies that are difficult or impossible to test directly (e.g., external APIs, databases, hardware devices).
    *   **Controlling Test Behavior:** Mocking allows you to control the behavior of mock objects, making it easier to test different scenarios and edge cases.

    You can use the `unittest.mock` module to create mock objects. The `unittest.mock` module provides several classes and functions for creating and configuring mock objects:

    *   `Mock`: A generic mock object.
    *   `MagicMock`: A mock object that implements most of the magic methods (e.g., `__len__`, `__iter__`).
    *   `patch`: A context manager and decorator that temporarily replaces an object with a mock object.

    Here's an example of using `unittest.mock` to mock an external API:

    ```python
    import unittest.mock
    import requests

    def get_data_from_api(url):
        """Gets data from an external API."""
        response = requests.get(url)
        response.raise_for_status()
        return response.json()

    def process_data(url):
        """Processes data from an external API."""
        data = get_data_from_api(url)
        # Perform some processing on the data
        return data['value'] * 2

    def test_process_data(monkeypatch):
        """Tests the process_data function with a mock API."""
        def mock_get_data_from_api(url):
            """A mock implementation of the get_data_from_api function."""
            return {'value': 10}

        monkeypatch.setattr("your_module.get_data_from_api", mock_get_data_from_api)

        result = process_data("http://example.com/api")
        assert result == 20
    ```

    In the example above you will use mocking via patching in the next section

    **Patching Objects and Functions:**

    The `patch` context manager and decorator can be used to temporarily replace an object or function with a mock object. This is a convenient way to mock dependencies for testing.

    ```python
    import unittest.mock
    import requests

    def get_data_from_api(url):
        """Gets data from an external API."""
        response = requests.get(url)
        response.raise_for_status()
        return response.json()

    def process_data(url):
        """Processes data from an external API."""
        data = get_data_from_api(url)
        # Perform some processing on the data
        return data['value'] * 2

    @unittest.mock.patch('your_module.get_data_from_api') #Change your module with your correct module
    def test_process_data(mock_get_data_from_api):
        """Tests the process_data function with a mock API."""
        mock_get_data_from_api.return_value = {'value': 10}

        result = process_data("http://example.com/api")
        assert result == 20

        mock_get_data_from_api.assert_called_once_with("http://example.com/api") #Test called endpoint
    ```

*   **13.4 Advanced Testing Techniques:**

    *   **Property-Based Testing (with Hypothesis):**

        Property-based testing (also known as generative testing or QuickCheck) is a testing technique in which you define properties that the code should satisfy, and then the testing framework automatically generates a large number of random test cases to verify that the properties hold.

        ```python
        # pip install hypothesis
        from hypothesis import given
        from hypothesis.strategies import integers

        def add(x, y):
            return x + y

        @given(integers(), integers())
        def test_add_is_commutative(x, y):
            assert add(x, y) == add(y, x)
        ```

    *   **Mutation Testing:**

        Mutation testing is a testing technique in which you introduce small changes (mutations) to the code and then run your tests to see if they catch the mutations. If your tests don't catch a mutation, it means that your tests are not comprehensive enough and need to be improved.

    *   **Integration Testing:**

        Integration tests verify the interactions between different parts of the system. Integration tests typically involve testing multiple units of code together, or testing the interactions between your code and external systems (e.g., databases, APIs).

    *   **Behavior-Driven Development (BDD):**

        Behavior-Driven Development (BDD) is a software development process that focuses on defining the expected behavior of the system in a clear and concise way. BDD uses a specific syntax for writing tests that describes the behavior of the system in terms of user stories and scenarios.
*   **13.5 Debugging Techniques and Tools:**

    *   **Using the `pdb` Debugger:**

        The `pdb` module is Python's built-in debugger. You can use `pdb` to set breakpoints, step through code, inspect variables, and more.

        To use `pdb`, you can insert the following line into your code:

        ```python
        import pdb; pdb.set_trace()
        ```

        When the code reaches this line, the debugger will start, and you can use various commands to inspect the state of your program:

        *   `n`: Step to the next line of code.
        *   `s`: Step into a function call.
        *   `c`: Continue execution until the next breakpoint.
        *   `p <variable>`: Print the value of a variable.
        *   `q`: Quit the debugger.

    *   **Using Logging for Debugging:**

        The `logging` module provides a way to record events that occur during the execution of your program. You can use logging to record errors, warnings, and informational messages.

        ```python
        import logging

        logging.basicConfig(level=logging.DEBUG)

        def my_function(x, y):
            logging.debug(f"my_function called with x={x}, y={y}")
            result = x + y
            logging.info(f"my_function returned {result}")
            return result
        ```

    *   **Using Profiling Tools to Identify Performance Bottlenecks:**

        Profiling tools can help you identify performance bottlenecks in your code. Python provides several profiling tools, such as `cProfile` and `line_profiler`.
    *   **Remote Debugging Techniques**

        If problems only occur in the deployment environment, you can enable debugging so you can remotely inspect the code.
*   **13.6 Common Debugging Mistakes and How to Avoid Them:**

    *   Syntax errors
    *   Runtime errors
    *   Logic errors
    *   ImportError
    *   NameError
    *   TypeError
    *   IndexError
    *   KeyError
    *   AttributeError
    *   ValueError
    *   OverflowError
    *   IOError

*   **13.7 Best Practices for Testing and Debugging:**

    *   Write tests early and often.
    *   Aim for high test coverage.
    *   Write clear and concise tests.
    *   Keep your tests independent and repeatable.
    *   Use descriptive test names.
    *   Use logging effectively for debugging.
    *   Learn to use a debugger effectively.
    *   Document all assumptions.
    *   Reproduce the bug first, then fix it.

*   **Project 13.1: Testing a Data Transformation Pipeline**

    Create unit tests for a data transformation pipeline that performs several data cleaning and feature engineering steps. Use `pytest` to write the tests and `unittest.mock` to mock external dependencies. Implement Property based test to increase the coverage of the code

    ```python
    import pytest
    import pandas as pd
    import numpy as np
    from sklearn.base import BaseEstimator, TransformerMixin
    from sklearn.exceptions import NotFittedError

    # Create the transformer
    class FeatureAdder(BaseEstimator, TransformerMixin):
        def __init__(self, feature_to_add):
            self.feature_to_add = feature_to_add
            self.fitted = False

        def fit(self, X, y=None):
            # Check that the columns exists
            if self.feature_to_add not in X.columns:
                raise ValueError('Column is not on X database!')
            self.fitted = True
            return self

        def transform(self, X, y=None):
            #Check first that the model is trully fitted
            if not self.fitted:
                 raise NotFittedError(self.__class__.__name__)
            X["newFeature"] = X[self.feature_to_add] * 2
            return X
    def test_feature_adder():
        #Sample values
        data = {'col1': [1, 2],
                'col2': [3, 4]}
        df = pd.DataFrame(data)
        #Model definition
        feature_adder = FeatureAdder("col1")
        #Fit our model
        feature_adder.fit(df)
        #Transform your data
        transformed_df =  feature_adder.transform(df)

        #Check values
        assert 'newFeature' in transformed_df.columns #validate added column
        assert transformed_df['newFeature'].tolist() == [2,4] #validate values

    def test_feature_adder_not_fited():
        data = {'col1': [1, 2],
                'col2': [3, 4]}
        df = pd.DataFrame(data)
        #Model definition
        feature_adder = FeatureAdder("col1")
        with pytest.raises(NotFittedError):
            feature_adder.transform(df)

    def test_feature_adder_incorrect_column():
        with pytest.raises(ValueError):
            #Sample values
            data = {'col1': [1, 2],
                    'col2': [3, 4]}
            df = pd.DataFrame(data)
            #Model definition
            feature_adder = FeatureAdder("col5") #invalid name

            #Fit our model
            feature_adder.fit(df)
            #Transform your data
            transformed_df =  feature_adder.transform(df)
    #Test with Hypotesis
    from hypothesis import given
    from hypothesis import strategies as st

    #You need to make sure to have valid values, due a multiplication
    @given(st.lists(st.integers(min_value=-100, max_value=100), min_size=2, max_size=2))
    def test_col1_values_hypotesis(col1_values):
        # Hypotesis is not made to assert raises a ValueError
        # It is for ensuring that if the columns are correct, the numbers are valid
        data = {'col1': col1_values,
                'col2': [3, 4]}
        df = pd.DataFrame(data)
        # Model definition
        feature_adder = FeatureAdder("col1")
        # Fit our model
        feature_adder.fit(df)
        # Transform your data
        transformed_df = feature_adder.transform(df)

        # Check values
        assert 'newFeature' in transformed_df.columns  # validate added column
        assert transformed_df['newFeature'].tolist() == [value * 2 for value in col1_values]  # validate values
    ```

*   **13.8 Conclusion:**

    This chapter provided a comprehensive overview of testing and debugging techniques in Python. We explored unit testing with pytest, mocking, and other advanced testing techniques, as well as debugging tools and best practices. By mastering these skills, you'll be well-equipped to write robust, reliable, and maintainable Python code.

