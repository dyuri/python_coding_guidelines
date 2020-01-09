# Python coding guidelines

Even if python 2 is (currently) the most common default version in many distributions, it isn't actively developed, and will not get even security updates after 2020, so I use python 3 for my projects.
By python 3 I mean the language version and not the CPython interpreter, for development I recommend to use it, but you can use other - more performance optimized, etc. - implementations like [PyPy](https://pypy.org/) (or Jython, IronPython or whatever).

## Style guide

### The Zen of Python ([PEP-20](https://www.python.org/dev/peps/pep-0020/))

```
Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one-- and preferably only one --obvious way to do it.
Although that way may not be obvious at first unless you're Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea -- let's do more of those!
```

### PEP-8

Try to follow the rules of [PEP-8](https://www.python.org/dev/peps/pep-0008/). (At least read it once.)

There are tools to help you ([flake8](http://flake8.pycqa.org/)) and there might be some exceptions where I break PEP-8 (default line length is 88 instead of 79), but in general it's a good idea to follow the rules.

### One statement per line

**Bad:**
```python
print 'one'; print 'two'

if x == 1: print 'one'
```

**Good:**
```python
print 'one'
print 'two'

if x == 1:
    print 'one'
```

### Global variables

Avoid them.

### Generator expressions / comprehensions

Prefer them in simple cases over for loops.

**Good:**
```python
result = [mapping_fn(x) for x in iterable] #  JS: iterable.map(mapping_fn)

result = [x for x in iterable if filter_fn(x)] # JS: iterable.filter(filter_fn)

# JS: iterable.filter(filter_fn).map(mapping_fn) - if position is required as well
result = [mapping_fn(x, i) for x, i in zip(iterable, range(len(iterable)) if filter_fn(x, i)]

# but *
result = []
for x in range(10):
    for y in range(10):
        if x * y > 10:
            result.append((x, y))
```

**Bad:**
```python
# same as *, for loop is easier to understand
[(x, y) for x in range(10) for y in range(10) if x * y > 10]
```

### Generators

Use generators where you can to save memory or CPU.

**Bad:**
```python
def squares_list(iterable):
    squares = []
    for i in iterable:
        squares.append(i*i)
    return squares
```

**Good:**
```python
def squares_gen(iterable):
    for i in iterable:
        yield i*i
```

### Lambda functions

Okay for one-liners, like mapping/filtering/sorting, etc.

Check the [operator module](https://docs.python.org/3/library/operator.html) if they are already available. (i.e. use `operator.mul` instead of `lambda x, y: x*y`).

### Conditional expressions

```python
# JS: let x = cond ? 1 : 2;
x = 1 if cond else 2
```

### Default argument values

Use only immutable values for default arguments, mutable objects can introduce tricky side-effects - they are initialized only at function definition.

**Good:**
```python
def foo(a, b=None):
    if b is None:
        b = []
    b.append(1)
```

**Bad:**
```python
def foo(a, b=[]):
    b.append(1)  # 1 will be appended to the list every time the function is called, [1, 1, 1, ...]
```

### Properties

Use properties for simple getters/setters.

**Example:**
```python
import math

class Square():

    def __init__(self, side):
        self.side = side

    @property
    def area(self):
        return self.side * self.side

    @area.setter
    def area_set(self, area):
        self.side = math.sqrt(area)

    # OR
    @property
    def perimeter(self):
        self._get_perimeter()

    def _get_perimeter(self):
        return self.side * 4
```

### True/false evaluation

Use _implicit_ false evaluation.

* `if foo` instead of `if foo != []`
* If comparing to singletons (i. e. `None`), use `is` or `is not`
* Never compare to `True` or `False`
* Prefer `if seq` to `if len(seq)`

### Decorators

Use `functools.wraps` to decorate exposed functions to properly copy function attributes.

**Bad:**
```python
def logit(f):
    def wrapper(*args, **kwargs):
        print(f'{f.__name__} called')
        return f(*args, **kwargs)
    return wrapper

@logit
def aplusb(a, b=1):
    return a + b

>>> aplusb(12)
aplusb called
13
>>> aplusb.__name__
'wrapper'
```

**Good:**
```python
from functools import wraps

def logit(f):
    @wraps(f)
    def wrapper(*args, **kwargs):
        print(f'{f.__name__} called')
        return f(*args, **kwargs)
    return wrapper

@logit
def aplusb(a, b=1):
    return a + b

>>> aplusb(12)
aplusb called
13
>>> aplusb.__name__
'aplusb'
```

### File access

Use the `with` statement, its more error-prone - closes files on exceptions for example.

**Bad:**
```python
f = open('file.txt', 'r')
data = f.read()
print(data)
f.close()
```

**Good:**
```python
with open('file.txt', 'r') as f:
    data = f.read()
    print(data)
```

### Trailing commas in sequences

Use trailing commas if the items are in separate lines.

**Example:**
```python
a = [1, 2, 3]  # no trailing comma
b = {
  "a": 1,
  "b": 2,
}
```

## Tools

### Flake8

Use [flake8](http://flake8.pycqa.org/) for following PEP-8 as much as possible. It can be integrated into most editors / IDEs.

I prefer `max-line-lenght` to be 88 instead of 79. (This is the default value for `black` as well.)

#### Configuration (~/.config/flake8)

```
[flake8]
max-line-length = 88
exclude = tests/*
max-complexity = 20
select = C,E,F,W,B,B950
ignore = E501
```

### Black

Use [black](https://github.com/ambv/black) to format your code before commiting it into a repository. It can be [integrated](https://black.readthedocs.io/en/stable/editor_integration.html) into most ediors / IDEs as well.

### pyenv

To support multiple python versions, you can use [pyenv](https://github.com/pyenv/pyenv) to have multiple (and stable) versions installed. Using [pyenv-virtualenv](https://github.com/pyenv/pyenv-virtualenv) it's easy to create virtualenvs based on a specific version of python.

### pytest

I like the simplicity of [pytest](http://pytest.org) tests. (w/ [pytest-cov](https://github.com/pytest-dev/pytest-cov) )

### tox

[tox](https://tox.readthedocs.io/) can automatically create virtualenvs for the supported python versions and test your application in them.

### Poetry

[Poetry](https://python-poetry.org/) is (imho) currently the best dependency management + build + packageing tool for python.

### mypy

[mypy](http://mypy-lang.org/) is a static type checker for python [type hints](https://www.python.org/dev/peps/pep-0484/). Using it helps to prevent errors that are caused by the dynamic typing behaviour of python.

## Framework preferences

* [Django](https://www.djangoproject.com/) - for full featured webapps with admin interface, user management and ORM for _free_
* [Flask](http://flask.pocoo.org/) - for lightweight webapps, APIs
* [Quart](http://pgjones.gitlab.io/quart/) - async (ASGI) framework with an API compatible with Flask (python 3.7+) - http2 and websocket support
