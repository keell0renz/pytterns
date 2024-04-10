<!--lint disable double-link-->

# Awesome Python Patterns 🐍

> A curated list of production-ready and real-world Python patterns.

## Contents

- [General](#general)
- [Documentation and Typing](#documentation-and-typing)
- [Object-Oriented Programming](#object-oriented-programming)
- [SDK Development](#sdk-development)
- [Back-End Development](#back-end-development)
- [CLI Applications](#cli-applications)
- [Generative AI](#generative-ai)

## General

### Managing resources which require cleanup

Encapsulate a resource which should be closed at the end in a class and implement
a context manager. Even if exception occurs, the resource still will be closed.
Useful for stuff like IO, files, sockets, connections, sessions.

```python
class Resource:
    def __enter__(self):
        print("Resource opened.")
        return self

    def __exit__(self, exc_type, exc_value, traceback):
        self.close()

    def close(self):
        print("Resource closed.")

with Resource() as r:
    print("Do stuff...")
```

### List, dict, generator and set comprehensions

These are useful for filling your data structures with some data in a concise way.

```python
x = [i for i in range(10)]
y = [j for j in range(10)]
xy = {i:str(j) for i, j in zip(x, y)}
s = {i%3 for i in range(10)}
```

Also, when you have to iterate through a big resource, such as a big file or dynamically generated data,
you may want to use generator comprehension. This way the big resource won't be loaded into memory just
outright, instead it will "fetch" it just in time, using less memory and resources.

```python
g = (2*x+5 for x in range(999999999))
```

### Type checking and Liskov substitution

Sometimes you will have different objects and you will have to perform checks on their type. You should not use ```==``` sign as it does not care about class inheritance. `isinstance()` function, on the contrary, respects class inheritance and should be used to perform type checks.

```python
class Parent():
    """Parent class. Like a general 'Car' class."""
    pass

class Child(Parent):
    """Child class. Like a specific car, Mercedes for example."""
    pass

c = Child()

if isinstance(c, Parent): # Is 'c' a car?
    print("'c' is a car!")
```

### `enumerate()` and `zip()`

Use `enumarate()` to iterate over list of elements together with indexes, and `zip()` to iterate over multiple sequences in the same time.

```python
a = [x for x in range(10)]

for i, v in enumerate(a):
    print(f"{i} -> {v}")
```
```python
b = ['a', 'b', 'c', 'd', 'e']
c = [1, 2, 3, 4, 5]

for letter, number in zip(b, c):
    print(f"{letter} is associated with {number}")
```

### Tracking execution time with `time.perf_counter()`

When you use standard `time.time()` for checking for how long your code executed
you risk getting imprecise results. Use `time.perf_counter()` specifically designed for this.

```python
import time

def time_counted():
    start = time.perf_counter()
    time.sleep(1)
    end = time.perf_counter()

    print(end - start)

time_counted()
```

### Separate CRUD and IO, especially if you need flexibility.

It is always good to separate CRUD access from IO, especially if you are writing code for other people, because they might need other ways to work with IO.

```python
import gzip
import typing

def io_logic(fp: typing.TextIO):
    fp.write("...")

def path_writing(path: str):
    with open(path, "w") as fp:
        io_logic(fp)

def other_writing():
    with gzip.open("example.txt.gz", "wt") as fp:
        io_logic(fp)
```

### Use `raise NotImplementedError` to make prototypes of functions.

When you are designing an interface of your function you may put `pass`, but at large scale you may forget it and get unexpected bugs without any warnings. Just use `raise NotImplementedError`!

```python
from typing import Any

def not_implemented(var: Any) -> None:
    raise NotImplementedError
```

### Use `Enum` to define a discrete set of values.

Use `Enum` to define a specific set of allowed variables. Good for documentation purposes.

```python
from enum import Enum

class VariableOptions(Enum):
    OPTION_ONE = 1
    OPTION_TWO = 2
    OPTION_THREE = 3
    OPTION_FOUR = 4

def choose_variable(option: VariableOptions):
    if option == VariableOptions.OPTION_ONE:
        return "Option One Selected"
    elif option == VariableOptions.OPTION_TWO:
        return "Option Two Selected"
    elif option == VariableOptions.OPTION_THREE:
        return "Option Three Selected"
    elif option == VariableOptions.OPTION_FOUR:
        return "Option Four Selected"
    else:
        return "Invalid Option"
```

## Documentation and Typing

The whole point of documentation and typing is to help other developers and yourself
later to work with your code. 90% of bugs and exceptions appear because of using incorrect datatypes, trying to access non-existend variables and data, etc. The best way
to prevent that is helping developer and the IDE to understand what your code takes and what returns.

```python
def function(v: list[float], a: float) -> list[float]:
    """
    Takes a vector `v` and multiplies it by scalar `a`, returns a vector.

    More in [docs](https://www.youtube.com/watch?v=dQw4w9WgXcQ).

    ```python
    function(
        v=[0.1, 0.2, 0.3, 0.4, 0.5],
        a=42
    )
    ```
    """

    return [a * x_i for x_i in v]
```

Remember that other people DO NOT KNOW your code. The best way to document a function is to:
* Properly type parameters and return value of function and/or class.
* Explain what function and/or class does, highlight parameter names.
* It is a good practice to leave a link to relevant external documentation.
* It is awesome to include a code snippet how function and/or class should work.
* Also, it is fantastic to give examples of data your function and/or class expects.

Similar stuff applies to classes, where you additionally tell about the class itself. Also it is a good idea to use `@property` so you could also document properties for other developers.

```python
class Circle:
    """This is a fancy Circle class."""

    def __init__(self, radius: float) -> None:
        self._radius = radius

    @property
    def radius(self) -> float:
        """Get the radius of the circle."""
        return self._radius

    @radius.setter
    def radius(self, value: float) -> None:
        """Set the radius of the circle."""
        if value >= 0:
            self._radius = value
        else:
            raise ValueError("Radius cannot be negative")

    @radius.deleter
    def radius(self) -> None:
        """Delete the radius of the circle."""
        del self._radius

    @property
    def diameter(self) -> float:
        """Compute the diameter of the circle."""
        return self._radius * 2
```

Also, it is a great practice to use `@dataclass` and `TypedDict`, they improve code readibility and help linters perform static checking and autocomplete. `@dataclass` is
useful as a lightweight data transfer object, but for more complex scenarios use `pydantic` schemas.

```python
from dataclasses import dataclass, astuple, asdict, field
from typing import Optional

@dataclass
class Comment:
    id: int
    text: Optional[str] = field(default="Deez")
    lst: list[str] = field(default_factory=list)
    
    sheesh: int = field(init=False)

    def __post_init__(self) -> None:
        self.sheesh = self.id * 1000
```

```python
from typing import TypedDict

class User(TypedDict):
    id: int
    name: str
    email: str
    is_active: bool

user: User = {
    'id': 1,
    'name': 'John Doe',
    'email': 'john.doe@example.com',
    'is_active': True
}

def display_user_info(user_info: User) -> None:
    print(f"User ID: {user_info['id']}")
    print(f"Name: {user_info['name']}")
    print(f"Email: {user_info['email']}")
    print(f"Active: {'Yes' if user_info['is_active'] else 'No'}")
```

## Object-Oriented Programming

Under construction 🛠️ ...

## SDK Development

Under construction 🛠️ ...

## Back-End Development

Under construction 🛠️ ...

## CLI Applications

Under construction 🛠️ ...
