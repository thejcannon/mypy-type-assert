# mypy-typing-asserts

Adds the ability to _assert_ types when typechecking to `mypy`.

```python
assert_type[MyType[tuple[int, ...]]](myobject.attribute)
```

## Support

`mypy-typing-asserts` is supported in `mypy >= 0.700`.

For `pyright` support, use `reveal_type(expression, expected_type=...)`. (See [this discussion](https://github.com/python/typing/discussions/1030#discussioncomment-1988476)). (Supported in version 1.1.211+)

## Installation

`mypy-typing-asserts` should be installed in to the same environment as your typechecker.

It can be installed by running `pip install mypy-typing-asserts`.

Alternatively if you're using `poetry`, `poetry add -D mypy-typing-asserts`.

## Usage

To use, just call `assert_type` providing a type-parameter and an argument. This will assert that
the type of the argument is __exactly__ the same type as the type-parameter (type-hierarchies are
not traversed) when typechecking (assuming you have enabled this functionality).

```python
from mypy_typing_asserts import assert_type

...

assert_type[MyType](my_expression)

# `assert_type` also returns the argument (but does no runtime checking)
var = assert_type[int](my_function())
```

Note that you may need to hide your import and usage behind `if typing.TYPE_CHECKING` if the
environment you're running the code in isn't the same that you typecheck in.

```python
from typing import TYPE_CHECKING

if TYPE_CHECKING:
    from mypy_typing_asserts import assert_type

...

if TYPE_CHECKING:
    assert_type[MyType](my_expression)
```

### Pitfalls

This plugin only gets executed for code that is being typechecked. Dependening on your configuration,
yopur typechecker might be skipping function bodies (e.g. `mypy` will skip unannotated function bodies
by default unless `--check-untyped-defs` is enabled).

If you're putting the `assert_type` calls inside a `pytest` test, make sure to annotate the `-> None`
return type to avoid this!

### Enabling the `mypy` plugin

In your mypy config, add `mypy_typing_asserts.mypy_plugin` to your `plugins` declaration.

See [mypy's documentation](https://mypy.readthedocs.io/en/stable/extending_mypy.html#configuring-mypy-to-use-plugins)


## Alternatives

All of the alternatives today ensure types are deduced correctly by running `mypy` in a subprocess,
which isn't always feasible or ideal. These include:

- [mypy-test](https://github.com/orsinium-labs/mypy-test) - standalone `mypy` wrapper
- [pytest-mypy-plugins](https://github.com/typeddjango/pytest-mypy-plugins) - pytest plugin, test cases described in a YAML file.
- [pytest-mypy-testing](https://github.com/davidfritzsche/pytest-mypy-testing) - pytest plugin, tests are described like pytest test cases (but they actually don't get run).
