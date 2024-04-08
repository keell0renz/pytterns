<!--lint disable double-link-->

# Awesome Python Patterns 🐍

> A curated list of production-ready and real-world Python patterns.

## Contents

- [General](#general)
- [Documentation and Typing](#documentation-and-typing)
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

## Documentation and Typing

Under construction 🛠️ ...

## SDK Development

Under construction 🛠️ ...

## Back-End Development

Under construction 🛠️ ...

## CLI Applications

Under construction 🛠️ ...

## Generative AI

Under construction 🛠️ ...
