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

Under construction 🛠️ ...

## Object-Oriented Programming

Under construction 🛠️ ...

## SDK Development

Under construction 🛠️ ...

## Back-End Development

Under construction 🛠️ ...

## CLI Applications

Under construction 🛠️ ...

## Generative AI

Under construction 🛠️ ...
