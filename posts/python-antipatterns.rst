.. title: Python antipatterns
.. slug: python-antipatterns
.. date: 2023-05-06 22:56:20 UTC
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text

Global variables
----------------
Global variables make code harder to reason about, test, and debug. Instead, use local variables or pass variables as function arguments.

.. code-block:: python


    x = 0

    def increment():
        global x
        x += 1

    print(x) # Output: 0

    increment()

    print(x) # Output: 1


Mutating arguments
------------------

Modifying arguments passed to a function can lead to unintended side effects and make code harder to understand. Instead, create a copy of the argument and modify the copy.
In Python, mutating arguments means modifying the value of an argument passed to a function. Here's an example of a function that mutates an argument

.. code-block:: python


    def add_one(numbers):

        for i in range(len(numbers)):
            numbers[i] += 1
        return numbers

    original_numbers = [1, 2, 3]
    new_numbers = add_one(original_numbers)

    print(original_numbers) # Output: [2, 3, 4]
    print(new_numbers) # Output: [2, 3, 4]

In this example, the add_one function takes a list of numbers as an argument. The function uses a for loop to iterate over the list and add 1 to each element. When the add_one function is called with the original_numbers list, the function modifies the list in place by adding 1 to each element.

The problem with this approach is that modifying the original list can lead to unintended side effects and make code harder to understand. To avoid this issue, it's better to create a new list inside the function and return the new list without modifying the original list. Here's an example of how to do this:

.. code-block:: python

    def add_one(numbers):
        new_numbers = [num + 1 for num in numbers]
        return new_numbers

    original_numbers = [1, 2, 3]
    new_numbers = add_one(original_numbers)

    print(original_numbers) # Output: [1, 2, 3]
    print(new_numbers) # Output: [2, 3, 4]

In this updated example, the add_one function creates a new list new_numbers by using a list comprehension to add 1 to each element of the numbers list. The function then returns the new list without modifying the original list. This approach is safer and makes the code easier to understand and maintain.

Using eval() or exec()
----------------------

Using eval() or exec() can be dangerous and allow arbitrary code execution. Instead, use safer alternatives like ast.literal_eval() or subprocess.
In Python, `eval()` and `exec()` are built-in functions that allow you to execute dynamic code. However, using them can be risky and potentially dangerous if not used properly. Here are some considerations when using `eval()` or `exec()`:

1. Security risks: Using `eval()` or `exec()` with untrusted input can lead to security vulnerabilities. If the input contains malicious code, it can be executed with the same privileges as the program itself, which can potentially harm the system.

2. Debugging issues: When using `eval()` or `exec()`, it can be difficult to debug issues that arise. The code is executed at runtime, which makes it harder to pinpoint the source of errors.

3. Performance impact: Using `eval()` or `exec()` can have a performance impact since the code is executed at runtime. If the code is executed frequently, it can slow down the program.

4. Readability: Code that uses `eval()` or `exec()` can be harder to read and understand since it's not immediately clear what the code will do at runtime.

5. Alternative solutions: In most cases, there are better and safer alternatives to using `eval()` or `exec()`. For example, instead of using `eval()` to execute a string as code, you can use a function that takes arguments and returns a value.

Here's an example of how to use `eval()`:

.. code-block:: python

    x = 1
    y = 2
    expression = 'x + y'
    result = eval(expression)
    print(result) # Output: 3


In this example, the `eval()` function is used to evaluate the string `'x + y'` as a Python expression. The values of `x` and `y` are substituted into the expression, and the result of the expression is returned. However, this code can be risky if the string `'x + y'` is supplied by user input since it can contain arbitrary code that can be executed with the same privileges as the program itself.

In general, it's best to avoid using `eval()` or `exec()` unless there is no other option. If you do need to use them, make sure to properly validate and sanitize input and limit the scope of execution as much as possible.



Not using with statements
-------------------------

Not using with statements for file I/O can lead to resource leaks and potential security vulnerabilities. Always use with statements to ensure that files are properly closed.
In Python, the `with` statement is used to ensure that a resource is properly managed and released, even if an exception occurs while the code is executing. Not using the `with` statement can lead to bugs, resource leaks, and other issues. Here's an example of how to use the `with` statement:

.. code-block:: python

    with open('file.txt', 'r') as f:
        data = f.read()
        # do something with data

    # the file is automatically closed when the 'with' block is exited


In this example, the `with` statement is used to open the file `'file.txt'` for reading. The code inside the `with` block reads the contents of the file into a variable `data`. When the block is exited, the file is automatically closed, even if an exception is raised while reading the file.

If you don't use the `with` statement to manage resources, you need to manually manage the resource yourself by opening and closing the resource explicitly. Here's an example of how to open and close a file without using the `with` statement:

.. code-block:: python

    f = open('file.txt', 'r')
    try:
        data = f.read()
        # do something with data
    finally:
        f.close()


In this example, the file is opened using the `open()` function and assigned to the variable `f`. The `try` block reads the contents of the file into a variable `data`. The `finally` block ensures that the file is closed after the `try` block is executed, even if an exception is raised.

While this approach works, using the `with` statement is generally considered to be cleaner and more readable. Additionally, the `with` statement ensures that the resource is properly managed and released, even if an exception is raised while the code is executing, making it more robust and less error-prone.

Ignoring exceptions

Ignoring exceptions can lead to hard-to-debug errors and security vulnerabilities. Always handle exceptions properly and provide meaningful error messages.
Ignoring exceptions in Python can lead to bugs and unexpected behavior, and it is generally considered an antipattern. When an exception is raised, it is usually an indication that something has gone wrong and needs to be addressed. Ignoring the exception can mask the underlying problem and make it harder to diagnose and fix the issue.

Here's an example of ignoring an exception:

.. code-block:: python

    try:
        # some code that may raise an exception
    except:
        pass


In this example, the `try` block contains code that may raise an exception. The `except` block catches any exception that is raised and ignores it, effectively doing nothing. This can lead to subtle bugs and unexpected behavior, as the exception may have important information about what went wrong.

Instead of ignoring exceptions, it's generally better to handle them in a meaningful way. Depending on the situation, you may want to log the exception, display an error message to the user, or take some other action to address the issue. Here's an example of handling an exception:

.. code-block:: python

    try:
        # some code that may raise an exception
    except SomeException as e:
        # handle the exception in a meaningful way
        log_error(e)
        display_error_message("An error occurred: {}".format(str(e)))


In this example, the `except` block catches a specific exception (`SomeException`) and handles it in a meaningful way. The exception is logged using a `log_error()` function, and an error message is displayed to the user using a `display_error_message()` function.

By handling exceptions in a meaningful way, you can make your code more robust and easier to maintain, as well as making it easier to diagnose and fix issues when they arise.


Overusing inheritance
---------------------
Overusing inheritance can make code harder to understand and maintain. Instead, favor composition and use inheritance only when it makes sense.
Overusing inheritance in Python can lead to overly complex and brittle code. Inheritance is a powerful tool for creating reusable code and building class hierarchies, but it should be used judiciously and with care.

One of the main issues with overusing inheritance is that it can lead to tightly-coupled code that is difficult to modify and maintain. When a subclass inherits from a superclass, it inherits all of the superclass's attributes and methods, which can make it harder to modify the subclass without affecting the superclass or other subclasses that inherit from it.

Additionally, overusing inheritance can make it harder to reuse code in different contexts. When a subclass inherits from a superclass, it is tightly coupled to the superclass's implementation, which can make it harder to reuse the subclass in different contexts or with different requirements.

To avoid overusing inheritance, it's important to follow the "composition over inheritance" principle. This means that instead of creating complex inheritance hierarchies, you should favor building objects out of smaller, more modular components. This approach allows for more flexible and reusable code, as each component can be reused and combined in different ways to meet different requirements.

Another way to avoid overusing inheritance is to favor delegation over inheritance. Delegation involves creating a new class that contains an instance of an existing class, and then exposing the existing class's functionality through the new class's methods. This approach allows for greater flexibility and reuse, as the new class can be modified and extended without affecting the existing class.

Overall, while inheritance can be a powerful tool, it should be used judiciously and with care. By following the "composition over inheritance" principle and favoring delegation over inheritance, you can create more flexible and reusable code that is easier to modify and maintain over time.

Hardcoding configuration values and paths
-----------------------------------------
Hardcoding configuration values can make code harder to reuse and maintain. Instead, use environment variables or configuration files to store configuration values.
Hardcoding paths to files and directories in your code can make it difficult to deploy your code to different environments.
Hardcoding configuration values and paths in Python can make your code inflexible and difficult to maintain. If a configuration value or path changes, you'll have to update your code to reflect the change, which can be time-consuming and error-prone. Additionally, hardcoding values can make it harder to reuse your code in different contexts or with different requirements.

To avoid hardcoding configuration values and paths in Python, you can use configuration files or environment variables. Configuration files can be used to store key-value pairs, which can be read into your Python code at runtime. Environment variables can be used to set values that your code can access through the `os.environ` dictionary.

Here's an example of using a configuration file to store database connection information:

.. code-block:: python

    import configparser

    config = configparser.ConfigParser()
    config.read('config.ini')

    db_host = config['database']['host']
    db_port = config['database']['port']
    db_user = config['database']['user']
    db_password = config['database']['password']

    # use the database connection information to connect to the database


In this example, the database connection information is stored in a configuration file called `config.ini`. The `ConfigParser` class is used to read the configuration file into a dictionary, which is then used to retrieve the database connection information.

Here's an example of using environment variables to store a path:

.. code-block:: python

    import os

    data_path = os.environ.get('MY_DATA_PATH', '/default/data/path')

    # use the data path in your code


In this example, the `os.environ` dictionary is used to retrieve the value of the `MY_DATA_PATH` environment variable. If the variable is not set, a default value of `/default/data/path` is used.
By using configuration files or environment variables to store configuration values and paths, you can make your code more flexible and easier to maintain. If a configuration value or path changes, you only need to update the configuration file or environment variable, rather than modifying your code. Additionally, configuration files and environment variables make it easier to reuse your code in different contexts or with different requirements.

Duplicated code
---------------

Duplicated code is code that is repeated in multiple places in your code. This can make your code difficult to maintain, as you may need to make changes to the code in multiple places if you need to update it.

Not using functions
-------------------
Functions are a powerful tool that can help you to organize your code and make it more readable and maintainable. Not using functions can make your code more difficult to understand and to debug.

Not using classes
-----------------
Classes are a powerful tool that can help you to create reusable objects. Not using classes can make your code more difficult to understand and to maintain.

Not using exceptions
--------------------
Exceptions are a powerful tool that can help you to handle errors gracefully. Not using exceptions can make your code more difficult to use and to debug.

Not using a debugger
--------------------
A debugger is a tool that can help you to step through your code and find errors. Not using a debugger can make it more difficult to find and fix errors in your code.

Using print for debugging
-------------------------
Using print statements for debugging can make it harder to debug and maintain code. Instead, use a debugger like pdb or ipdb to step through code and inspect variables.

Not using type annotations
--------------------------

Python 3 introduced type annotations, which can help catch bugs at compile-time and make code more self-documenting. Not using type annotations can lead to code that is harder to understand and maintain.
Not using type annotations in Python can make your code harder to read, understand, and maintain. Type annotations allow you to specify the types of function arguments and return values, which can help catch bugs early, improve code clarity, and make it easier for others to use and understand your code.

Here's an example of a function with type annotations:

.. code-block:: python

    def add_numbers(x: int, y: int) -> int:
        return x + y

In this example, the `add_numbers` function takes two arguments, `x` and `y`, both of which are expected to be integers. The function returns an integer as well. By using type annotations, you can make it clear to anyone reading your code what types of arguments the function expects and what type of value it returns.

Type annotations can also be used for class attributes and instance variables. Here's an example:

.. code-block:: python

    class Person:
        name: str
        age: int

        def __init__(self, name: str, age: int):
            self.name = name
              self.age = age


In this example, the `Person` class has two attributes, `name` and `age`, both of which are expected to be of specific types. By using type annotations for class attributes and instance variables, you can make it clear to anyone using your class what types of values they should provide.

Type annotations can be especially useful in larger codebases or when working on a team, as they can help catch type-related bugs early and make it easier for team members to understand each other's code.

To use type annotations in Python, you'll need to use Python 3.5 or later. Type annotations are not enforced by the Python interpreter, but you can use tools like `mypy` to check your code for type-related errors at runtime.


Not using f-strings
-------------------
Python 3.6 introduced f-strings, which provide an easy and concise way to format strings. Not using f-strings can make code harder to read and maintain.

Not using f-strings in Python can make your code less readable and harder to maintain. f-strings are a powerful feature introduced in Python 3.6 that allow you to easily format strings with variables or expressions.

Here's an example of a string formatting without f-strings:

.. code-block:: python

    name = 'John'
    age = 30

    print('My name is %s and I am %d years old.' % (name, age))


In this example, we're using the `%` operator to format the string with the variables `name` and `age`. While this method works, it can be confusing and error-prone, especially with complex formatting.

Here's the same example using f-strings:

.. code-block:: python

    name = 'John'
    age = 30

    print(f'My name is {name} and I am {age} years old.')

In this example, we're using f-strings to format the string with the variables `name` and `age`. F-strings allow us to embed expressions inside curly braces `{}` within a string, making the code more concise and easier to read.

F-strings also allow for complex expressions, making them more versatile than other string formatting methods. Here's an example:

.. code-block:: python

    num1 = 10
    num2 = 20

    print(f'The sum of {num1} and {num2} is {num1+num2}.')

In this example, we're using an f-string to format the string with the variables `num1` and `num2`, as well as an expression to calculate their sum.

In summary, using f-strings in Python can make your code more readable, concise, and easier to maintain. F-strings are a powerful feature that allows you to format strings with variables and expressions in a more intuitive and error-free way.

Not using enumerate
-------------------
Not using enumerate to loop over a sequence and get both the index and value can make code harder to read and maintain. Instead, use enumerate to loop over a sequence and get both the index and value.

Using `enumerate` in Python can make your code more readable and easier to maintain. `enumerate` is a built-in Python function that allows you to loop over an iterable and keep track of the index of the current element.

Here's an example of using `enumerate` to loop over a list and keep track of the index:

.. code-block:: python

    fruits = ['apple', 'banana', 'orange']

    for index, fruit in enumerate(fruits):
        print(f'Fruit {index}: {fruit}')

In this example, we're using `enumerate` to loop over the `fruits` list and keep track of the index of each fruit. The `enumerate` function returns a tuple with the index and the value of each element, which we're unpacking into the variables `index` and `fruit`. We then print a formatted string that includes the index and the value of each element.

Using `enumerate` can make your code more readable and easier to understand, especially when you need to loop over an iterable and keep track of the index. Without `enumerate`, you would need to manually create a counter variable and increment it in each iteration of the loop, which can be error-prone and make the code harder to read.

Here's an example of achieving the same result as the previous example without using `enumerate`:

.. code-block:: python

    fruits = ['apple', 'banana', 'orange']
    index = 0

    for fruit in fruits:
        print(f'Fruit {index}: {fruit}')
        index += 1

In this example, we're manually creating a counter variable `index` and incrementing it in each iteration of the loop. The resulting output is the same as the previous example, but the code is longer and harder to read.

In summary, using `enumerate` in Python can make your code more readable and easier to maintain, especially when you need to loop over an iterable and keep track of the index. Using `enumerate` can also help you avoid errors and make your code more concise.

Not using context managers
--------------------------

Not using context managers can lead to resource leaks and potential security vulnerabilities. Always use context managers to ensure that resources are properly closed.

Not using the else clause with for and while
--------------------------------------------
In Python, you can use the `else` clause with a `for` or `while` loop to specify a block of code that should be executed if the loop completes normally without encountering a `break` statement. This can be a powerful tool for creating more robust and reliable code.

Here's an example of using the `else` clause with a `for` loop:

.. code-block:: python

    for i in range(5):
        print(i)
    else:
        print("Loop completed normally")

In this example, we're using a `for` loop to print the values `0` through `4`. After the loop completes, we're using the `else` clause to print a message indicating that the loop completed normally. If we had used a `break` statement inside the loop to exit early, the `else` clause would not be executed.

Here's an example of using the `else` clause with a `while` loop:

.. code-block:: python

    i = 0
    while i < 5:
        print(i)
        i += 1
    else:
        print("Loop completed normally")

In this example, we're using a `while` loop to print the values `0` through `4`. After the loop completes, we're using the `else` clause to print a message indicating that the loop completed normally. Again, if we had used a `break` statement inside the loop to exit early, the `else` clause would not be executed.

Using the `else` clause with a `for` or `while` loop can make your code more robust and reliable, especially when you need to ensure that the loop completes normally without encountering errors or unexpected conditions. By providing a block of code to be executed only if the loop completes normally, you can create more robust and maintainable code.


Not using the else clause with for and while can make code harder to read and maintain. Instead, use the else clause with for and while to execute code when the loop completes normally.

Using list as a default argument value
--------------------------------------

Using list as a default argument value can lead to unexpected behavior when the list is modified. Instead, use None as the default argument value and create a new list inside the function if needed.
In Python, you can use a list as a default argument value in a function. While this can be useful in some cases, it can also lead to unexpected behavior if you're not careful.

Here's an example of using a list as a default argument value:

.. code-block:: python

    def add_item(item, lst=[]):
        lst.append(item)
        return lst

    print(add_item(1))
    print(add_item(2))

In this example, we have a function `add_item` that takes an `item` argument and an optional `lst` argument, which defaults to an empty list. The function appends the `item` to the `lst` and returns the updated list. We then call the function twice, once with the argument `1` and once with the argument `2`.

The output of this code is:

.. code-block:: python

    [1]
    [1, 2]


This behavior may be surprising if you're not expecting it. The reason for this is that Python only evaluates the default argument value once, when the function is defined. In this case, the default value for `lst` is an empty list, which is created once when the function is defined. Each time the function is called without a value for `lst`, the same list object is used and modified by the function.

To avoid this issue, you can use `None` as the default value for the argument and create a new list inside the function if the argument is `None`. Here's an example of how to do this:

.. code-block:: python

    def add_item(item, lst=None):
        if lst is None:
            lst = []
        lst.append(item)
        return lst

    print(add_item(1))
    print(add_item(2))

In this example, we're checking if `lst` is `None` inside the function and creating a new list if it is. This ensures that a new list is created each time the function is called without a value for `lst`.

In summary, using a list as a default argument value in Python can lead to unexpected behavior if you're not careful. To avoid this, you can use `None` as the default value and create a new list inside the function if the argument is `None`. This ensures that a new list is created each time the function is called without a value for the argument.